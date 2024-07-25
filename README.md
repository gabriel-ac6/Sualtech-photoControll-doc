### Documentação de Manutenção de Código

Este documento fornece instruções detalhadas sobre a manutenção do código do sistema de navegação de tabs em um aplicativo Ionic.

#### Estrutura de Tabs no Ionic

O arquivo `tabs.page.html` define a estrutura das tabs do aplicativo, utilizando componentes do Ionic. As tabs são exibidas na parte inferior da tela e mudam conforme o status de login do usuário. 

```html
<ion-tabs>
  <ion-tab-bar slot="bottom">
    <ion-tab-button *ngIf="!(isLoggedIn | async)" tab="tab1" href="/tabs/tab1">
      <ion-icon aria-hidden="true" name="log-in-outline"></ion-icon>
      <ion-label>Login</ion-label>
    </ion-tab-button>

    <ion-tab-button *ngIf="isLoggedIn | async" (click)="deleteAllSessions()">
      <ion-icon aria-hidden="true" name="log-out-outline"></ion-icon>
      <ion-label>Logoff</ion-label>
    </ion-tab-button>

    <ion-tab-button tab="tab2" href="/tabs/tab2">
      <ion-icon aria-hidden="true" name="camera-outline"></ion-icon>
      <ion-label>Tirar Foto</ion-label>
    </ion-tab-button>
  </ion-tab-bar>
</ion-tabs>
```

O componente `<ion-tabs>` é o contêiner principal para as tabs. A `<ion-tab-bar>` é a barra de tabs na parte inferior, e `<ion-tab-button>` são os botões para navegação entre as tabs. O botão de login aparece se o usuário não estiver logado, o botão de logoff aparece se o usuário estiver logado, e o botão "Tirar Foto" está sempre visível.

O arquivo `tabs.page.ts` define a lógica por trás da estrutura de tabs e o gerenciamento do estado de login.

```typescript
import { Component, EnvironmentInjector, inject, OnInit } from '@angular/core';
import { IonTabs, IonTabBar, IonTabButton, IonIcon, IonLabel, ToastController } from '@ionic/angular/standalone';
import { addIcons } from 'ionicons';
import { logOutOutline, logInOutline, cameraOutline } from 'ionicons/icons';
import { Router } from '@angular/router';
import { BehaviorSubject } from 'rxjs';
import { CommonModule } from '@angular/common';
import { HttpClient, HttpHeaders, HttpResponse, HttpErrorResponse } from '@angular/common/http';
import { throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Component({
  selector: 'app-tabs',
  templateUrl: 'tabs.page.html',
  styleUrls: ['tabs.page.scss'],
  standalone: true,
  imports: [IonTabs, IonTabBar, IonTabButton, IonIcon, IonLabel, CommonModule],
})
export class TabsPage implements OnInit {
  public environmentInjector = inject(EnvironmentInjector);
  isLoggedIn: BehaviorSubject<boolean> = new BehaviorSubject<boolean>(false);

  constructor(private router: Router, private http: HttpClient, private toastController: ToastController) {
    addIcons({ logOutOutline, logInOutline, cameraOutline });
  }

  ngOnInit() {
    this.checkLoginStatus();
  }

  checkLoginStatus() {
    const sessionToken = sessionStorage.getItem('sessionToken');
    this.isLoggedIn.next(!!sessionToken);
  }

  async presentToast(message: string, color: string) {
    const toast = await this.toastController.create({
      message,
      duration: 2000,
      color
    });
    toast.present();
  }

  public deleteAllSessions() {
    sessionStorage.removeItem('sessionToken');
    sessionStorage.clear();

    this.isLoggedIn.next(false);

    this.http.delete('http://localhost:8081/auth/session', { responseType: 'text' })
      .pipe(
        catchError((error: HttpErrorResponse) => {
          console.error('Erro ao deletar as sessões no servidor:', error);
          return throwError(() => new Error('Erro ao deletar as sessões no servidor'));
        })
      )
      .subscribe(
        response => {
          console.log('Sessões deletadas com sucesso no servidor:', response);
          this.router.navigate(['/tabs/tab1']);
        },
        error => {
          console.error('Erro ao deletar as sessões no servidor:', error);
        }
      );
  }
}
```

Este arquivo importa componentes do Angular e Ionic, além de ícones e módulos comuns. A classe `TabsPage` implementa a lógica principal da página de tabs. As propriedades incluem `environmentInjector`, que é um injetor de ambiente, e `isLoggedIn`, um `BehaviorSubject` para controlar o estado de login. No construtor, são injetados `Router`, `HttpClient` e `ToastController`, e os ícones são adicionados usando `addIcons`.

* O método `ngOnInit` chama `checkLoginStatus` ao iniciar, que verifica o token de sessão no `sessionStorage` e atualiza `isLoggedIn`. 
* O método `presentToast` exibe uma notificação toast. 
* O método `deleteAllSessions` remove o token de sessão, limpa o `sessionStorage`, atualiza `isLoggedIn` e faz uma requisição DELETE para deletar as sessões no servidor.

O arquivo `tabs.route.ts` define as rotas do aplicativo, incluindo a proteção de rotas usando um `AuthGuard`.

```typescript
import { Routes } from '@angular/router';
import { TabsPage } from './tabs.page';
import { AuthGuard } from '../auth.guard';

export const routes: Routes = [
  {
    path: 'tabs',
    component: TabsPage,
    children: [
      {
        path: 'tab1',
        loadComponent: () =>
          import('../tab1/tab1.page').then((m) => m.Tab1Page),
      },
      {
        path: 'tab2',
        loadComponent: () =>
          import('../tab2/tab2.page').then((m) => m.Tab2Page),
          canActivate: [AuthGuard]
      },
      {
        path: '',
        redirectTo: '/tabs/tab1',
        pathMatch: 'full',
      },
    ],
  },
  {
    path: '',
    redirectTo: '/tabs/tab1',
    pathMatch: 'full',
  },
];
```

Este arquivo inclui `Routes`, `TabsPage` e `AuthGuard`. A rota `tabs` contém as rotas filhas `tab1` e `tab2`, onde `tab2` está protegida pelo `AuthGuard`. Há uma rota padrão que redireciona para `/tabs/tab1`.

#### Notas de Manutenção

1. **Verificações Regulares**: Certifique-se de que os ícones importados estejam atualizados. Verifique se o servidor de autenticação está funcionando corretamente e as requisições DELETE estão sendo processadas como esperado. Garanta que o `AuthGuard` esteja configurado corretamente para proteger rotas sensíveis.
2. **Atualizações de Dependências**: Mantenha as dependências do Angular, Ionic e outras bibliotecas atualizadas para garantir compatibilidade e segurança.
3. **Gerenciamento de Sessões**: Revise regularmente o fluxo de login e logout para assegurar que as sessões estão sendo gerenciadas corretamente no `sessionStorage`.
4. **Tratamento de Erros**: Implemente loggers adicionais se necessário para capturar e monitorar erros na aplicação, especialmente durante operações de rede.
5. **Testes**: Teste a aplicação em diferentes navegadores e dispositivos para garantir que a funcionalidade de tabs e login/logout esteja funcionando corretamente. Escreva testes unitários e de integração para cobrir os cenários principais, como login, logout e navegação entre tabs.
