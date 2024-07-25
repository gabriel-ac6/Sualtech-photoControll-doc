### Documentação de Manutenção de Código

Este documento fornece instruções detalhadas sobre a manutenção do código do sistema de navegação de tabs em um aplicativo Ionic.

----

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

#### Notas de Manutenção para a Tabs

1. **Verificações Regulares**: Certifique-se de que os ícones importados estejam atualizados. Verifique se o servidor de autenticação está funcionando corretamente e as requisições DELETE estão sendo processadas como esperado. Garanta que o `AuthGuard` esteja configurado corretamente para proteger rotas sensíveis.
2. **Atualizações de Dependências**: Mantenha as dependências do Angular, Ionic e outras bibliotecas atualizadas para garantir compatibilidade e segurança.
3. **Gerenciamento de Sessões**: Revise regularmente o fluxo de login e logout para assegurar que as sessões estão sendo gerenciadas corretamente no `sessionStorage`.
4. **Tratamento de Erros**: Implemente loggers adicionais se necessário para capturar e monitorar erros na aplicação, especialmente durante operações de rede.
5. **Testes**: Teste a aplicação em diferentes navegadores e dispositivos para garantir que a funcionalidade de tabs e login/logout esteja funcionando corretamente. Escreva testes unitários e de integração para cobrir os cenários principais, como login, logout e navegação entre tabs.

----

#### Estrutura de Tabs1 (tela de login) no Ionic



----

#### Estrutura de Tabs2 (tela de tirar foto) no Ionic


### Documentação do Código


#### Métodos do Componente




# Documentação Detalhada do `tab2.page.ts`

O componente `Tab2Page` gerencia a captura de fotos e a navegação por slides de instruções. Este componente é usado em uma aplicação Ionic e utiliza Angular para a lógica e Swiper para a navegação entre os slides.

## Importações e Configurações do Componente

```typescript
import { Component, ViewChild, ElementRef, OnInit, OnDestroy, AfterViewInit } from '@angular/core';
import { DomSanitizer, SafeResourceUrl } from '@angular/platform-browser';
import { CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HttpClient, HttpHeaders, HttpErrorResponse } from '@angular/common/http';
import { PhotoService } from './photo.service';
import Swiper from 'swiper';
import { catchError } from 'rxjs/operators';
import { Router } from '@angular/router';
import { throwError } from 'rxjs';
```

### Decorador `@Component`
- **selector**: Define o seletor para o componente, que é `app-tab2`.
- **templateUrl**: Define o caminho para o arquivo de template HTML do componente (`tab2.page.html`).
- **standalone**: Marca o componente como independente, sem necessidade de módulo Angular separado.
- **imports**: Especifica os módulos Angular utilizados, neste caso `CommonModule`.
- **styleUrls**: Define o caminho para o arquivo de estilos CSS do componente (`tab2.page.scss`).
- **schemas**: Permite o uso de elementos personalizados no HTML. `CUSTOM_ELEMENTS_SCHEMA` é usado para permitir elementos personalizados.

## Propriedades do Componente

### Variáveis de Controle de Estado

- `photo: SafeResourceUrl | null`: Armazena a foto capturada e formatada de forma segura para exibição.
- `isDarkTheme: boolean`: Controla o tema da aplicação (claro ou escuro).
- `showInstructions: boolean`: Indica se as instruções estão visíveis.
- `isPhotoTaken: boolean`: Controla se uma foto foi tirada ou não.
- `stream: MediaStream | undefined`: Armazena o stream da câmera para a visualização do vídeo.
- `modalStream: MediaStream | undefined`: Armazena o stream da câmera quando a modal está aberta.
- `isPrevButtonDisabled: boolean`: Controla se o botão de navegação anterior está desativado.
- `isNextButtonDisabled: boolean`: Controla se o botão de navegação seguinte está desativado.
- `isSlideDraggingDisabled: boolean`: Controla se a funcionalidade de arrastar os slides está desativada.
- `progressBarWidth: string`: Controla a largura da barra de progresso.
- `remainingTime: number`: Tempo restante para a atualização dos botões.
- `isCameraModalOpen: boolean`: Indica se a modal da câmera está aberta ou não.
- `photoUrl: string | undefined`: URL da foto capturada para exibição na modal.

## Método `ngOnInit`

- **Objetivo**: Configura o estado inicial do componente quando ele é criado.
- **Descrição**:
  - Verifica se as instruções estão visíveis. Se não, inicia a câmera.
  - Desativa os botões e inicia a barra de progresso, que será atualizada após 3 segundos.

```typescript
  ngOnInit() {
    if (!this.showInstructions) {
      this.startCamera();
    }

    this.disableButtons();
    this.startProgressBar();
    setTimeout(() => {
      this.enableButtons();
    }, 3000);
  }
```

## Método `ngAfterViewInit`

- **Objetivo**: Configura a visualização após a inicialização completa da view.
- **Descrição**:
  - Configura o evento de mudança de slide para o Swiper.
  - Inicia a câmera se as instruções não estiverem visíveis.

```typescript
  ngAfterViewInit() {
    if (this.swiperEx) {
      this.swiperEx.nativeElement.swiper.on('slideChange', () => {
        this.onSlideChange();
      });
    }
    if (!this.showInstructions) {
      this.startCamera();
    }
    console.log('Câmera iniciada');
  }
```

## Método `ngOnDestroy`

- **Objetivo**: Limpa recursos quando o componente é destruído.
- **Descrição**: 
  - Para a câmera e a câmera da modal para liberar recursos.

```typescript
  ngOnDestroy() {
    this.stopCamera();
    this.stopModalCamera();
  }
```

## Métodos da Tela de Captura de Foto 

### Método `takePicture`

- **Objetivo**: Captura uma imagem do vídeo em execução.
- **Descrição**:
  - Define as dimensões do canvas para corresponder ao vídeo.
  - Desenha o vídeo no canvas e converte a imagem para um Data URL em formato JPEG.
  - Atualiza o estado para mostrar a foto capturada.

```typescript
  async takePicture() {
    const video = this.videoElement.nativeElement;
    const canvas = this.canvasElement.nativeElement;
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    const context = canvas.getContext('2d');

    if (context) {
      context.drawImage(video, 0, 0, canvas.width, canvas.height);
      const dataUrl = canvas.toDataURL('image/jpeg');
      this.photo = this.sanitizer.bypassSecurityTrustResourceUrl(dataUrl);
      this.isPhotoTaken = true;
      console.log('Base64 Image:', dataUrl.split(',')[1]);
    } else {
      console.error('Contexto é nulo');
    }
  }
```

### Método `sendPhoto`

- **Objetivo**: Envia a foto capturada para o servidor.
- **Descrição**:
  - Extraí o Base64 da imagem do Data URL.
  - Envia a imagem para o servidor utilizando uma requisição HTTP POST.
  - Limpa a foto e fecha a modal após o envio.

```typescript
  async sendPhoto() {
    if (this.photo) {
      const dataUrl = (this.photo as any).changingThisBreaksApplicationSecurity as string;
      const base64Image = dataUrl.split(',')[1];

      console.log('Base64 Image:', base64Image);

      try {
        await this.uploadPhoto(base64Image);
        console.log('Foto enviada com sucesso!');
        this.deleteAllSessions();
        this.photo = null;
        this.closeCameraModal();
      } catch (error) {
        console.error('Erro ao enviar a foto:', error);
      }
    } else {
      console.error('Nenhuma foto disponível para envio.');
    }
  }
```

## Métodos de Controle da Câmera

### Método `startCamera`

- **Objetivo**: Inicia a câmera do dispositivo.
- **Descrição**:
  - Solicita permissão para usar a câmera.
  - Define o stream da câmera como a fonte do elemento de vídeo.
  - Inicia a reprodução do vídeo.

```typescript
  async startCamera() {
    try {
      this.stream = await navigator.mediaDevices.getUserMedia({ video: true });
      const videoElement = this.videoElement.nativeElement;
      videoElement.srcObject = this.stream;
      videoElement.play();
    } catch (err) {
      console.error('Erro ao acessar a câmera:', err);
      this.photoService.deleteAllSessions();
    }
  }
```

### Método `stopCamera`

- **Objetivo**: Para a câmera e libera recursos.
- **Descrição**: 
  - Para todos os tracks do stream da câmera.

```typescript
  stopCamera() {
    if (this.stream) {
      this.stream.getTracks().forEach(track => track.stop());
      this.stream = undefined;
    }
  }
```

### Método `startModalCamera`

- **Objetivo**: Inicia a câmera na modal.
- **Descrição**:
  - Solicita permissão para usar a câmera.
  - Define o stream da câmera como a fonte do vídeo na modal.
  - Inicia a reprodução do vídeo.

```typescript
  async startModalCamera() {
    try {
      this.modalStream = await navigator.mediaDevices.getUserMedia({ video: true });
      const modalVideoElement = this.modalVideoElement.nativeElement;
      modalVideoElement.srcObject = this.modalStream;
      modalVideoElement.play();
    } catch (err) {
      console.error('Erro ao acessar a câmera na modal:', err);
    }
  }
```

### Método `stopModalCamera`

- **Objetivo**: Para a câmera da modal e libera recursos.
- **Descrição**:
  - Para todos os tracks do stream da câmera da modal.

```typescript
  stopModalCamera() {
    if (this.modalStream) {
      this.modalStream.getTracks().forEach(track => track.stop());
      this.modalStream = undefined;
    }
  }
```

## Métodos de Navegação entre Slides

### Método `onSlideChange`

- **Objetivo**: Manipula mudanças de slide no Swiper.
- **Descrição**:
  - Desativa os botões de navegação e inicia a barra de progresso.

```typescript
  onSlideChange() {
    console.log(this.swiperEx?.nativeElement.swiper.activeIndex);
    this.disableButtons();
    this.startProgressBar();
    setTimeout(() => {
      this.enableButtons();
    }, 3000);
  }
```

### Método `onSlidePrev`

- **Objetivo**: Navega para o slide anterior.
- **Descrição**:
  - Desativa os botões de navegação

 e inicia a barra de progresso.

```typescript
  onSlidePrev() {
    if (!this.isPrevButtonDisabled) {
      this.swiperEx?.nativeElement.swiper.slidePrev();
      this.disableButtons();
      this.startProgressBar();
      setTimeout(() => {
        this.enableButtons();
      }, 3000);
    }
  }
```

### Método `onSlideNext`

- **Objetivo**: Navega para o próximo slide.
- **Descrição**:
  - Desativa os botões de navegação e inicia a barra de progresso.

```typescript
  onSlideNext() {
    if (!this.isNextButtonDisabled) {
      this.swiperEx?.nativeElement.swiper.slideNext();
      this.disableButtons();
      this.startProgressBar();
      setTimeout(() => {
        this.enableButtons();
      }, 3000);
    }
  }
```

## Outros Métodos e Utilitários

### Método `toggleVisibility`

- **Objetivo**: Alterna a visibilidade das instruções e controla o estado da câmera.
- **Descrição**:
  - Se os termos de uso foram aceitos, alterna a visibilidade das instruções e controla a câmera conforme o estado.

```typescript
  toggleVisibility() {
    if (this.isTermsAccepted) {
      this.showInstructions = !this.showInstructions;
      if (!this.showInstructions) {
        this.startCamera();
      } else {
        this.stopCamera();
      }
    } else {
      alert('Você deve aceitar os termos de uso para prosseguir.');
    }
  }
```

### Método `uploadPhoto`

- **Objetivo**: Envia a foto capturada para o servidor.
- **Descrição**:
  - Configura o cabeçalho da requisição.
  - Envia a foto como uma requisição POST para o endpoint especificado.

```typescript
  async uploadPhoto(base64Image: string): Promise<void> {
    const headers = new HttpHeaders({ 'Content-Type': 'application/json' });
    try {
      const response = await this.http.post('https://localhost:8081/profile/photo', { image: base64Image }, { headers }).toPromise();
      console.log('Upload Response:', response);
    } catch (error) {
      console.error('Erro ao enviar a foto:', error);
      throw error;
    }
  }
```

### Método `startProgressBar`

- **Objetivo**: Inicia a barra de progresso para os slides.
- **Descrição**:
  - Atualiza a largura da barra de progresso com base no tempo decorrido.

```typescript
  startProgressBar() {
    const totalTime = 3000; // Tempo total para a barra de progresso
    const startTime = Date.now();

    const updateProgress = () => {
      const elapsedTime = Date.now() - startTime;
      const progress = Math.min((elapsedTime / totalTime) * 100, 100);
      this.progressBarWidth = `${progress}%`;

      if (progress < 100) {
        requestAnimationFrame(updateProgress);
      }
    };

    updateProgress();
  }
```

### Método `disableButtons`

- **Objetivo**: Desativa os botões de navegação.
- **Descrição**:
  - Define as propriedades `isPrevButtonDisabled` e `isNextButtonDisabled` para `true`.

```typescript
  disableButtons() {
    this.isPrevButtonDisabled = true;
    this.isNextButtonDisabled = true;
  }
```

### Método `enableButtons`

- **Objetivo**: Habilita os botões de navegação.
- **Descrição**:
  - Define as propriedades `isPrevButtonDisabled` e `isNextButtonDisabled` para `false`.

```typescript
  enableButtons() {
    this.isPrevButtonDisabled = false;
    this.isNextButtonDisabled = false;
  }
```

### Método `deleteAllSessions`

- **Objetivo**: Limpa todas as sessões ativas.
- **Descrição**:
  - Chama o serviço `PhotoService` para deletar todas as sessões.

```typescript
  deleteAllSessions() {
    this.photoService.deleteAllSessions();
  }
```

### Método `closeCameraModal`

- **Objetivo**: Fecha a modal da câmera.
- **Descrição**:
  - Define `isCameraModalOpen` para `false` e para a câmera da modal.

```typescript
  closeCameraModal() {
    this.isCameraModalOpen = false;
    this.stopModalCamera();
  }
```

---

Essa documentação fornece uma visão detalhada do layout, funcionalidade e comportamento do componente `Tab2Page`.
