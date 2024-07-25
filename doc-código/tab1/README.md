# Documentação do Tab1 (Tela de Login)


## Documentação do Código HTML

### Cabeçalho (`ion-header`)

#### Estrutura
```html
<ion-header [translucent]="true">
  <ion-toolbar>
    <ion-title style="text-align: center;">Login</ion-title>
    <ion-buttons slot="end">
      <ion-button (click)="toggleTheme()">
        <ion-icon [name]="isDarkTheme ? 'sunny-outline' : 'moon-outline'"></ion-icon>
      </ion-button>
    </ion-buttons>
    <ion-buttons slot="start">
      <span style="background-color: black; width: 63px; border-radius: 4pt;">
        <ion-img src="../../assets/logo-sualtech.png" alt="Logo da Stell Tech"></ion-img>
      </span>
    </ion-buttons>
  </ion-toolbar>
</ion-header>
```

#### Descrição

- **`<ion-header>`**: Define o cabeçalho da página, com a propriedade `[translucent]="true"` para tornar o cabeçalho semi-transparente.
- **`<ion-toolbar>`**: Contém a barra de ferramentas do cabeçalho.
- **`<ion-title>`**: Exibe o título "Login" centralizado no cabeçalho.
- **`<ion-buttons slot="end">`**: Adiciona um botão no final do cabeçalho.
  - **Botão de Tema**: Alterna entre o modo claro e escuro. Usa um ícone que muda entre `sunny-outline` (modo claro) e `moon-outline` (modo escuro) com base na variável `isDarkTheme`.
- **`<ion-buttons slot="start">`**: Adiciona um botão no início do cabeçalho.
  - **Logo**: Exibe a imagem do logo da empresa com um fundo preto.

### Conteúdo (`ion-content`)

#### Estrutura
```html
<ion-content>
  <div style="display: flex; justify-content: center; align-items: center; height: 100%; background-image: url('../assets/teste4.png'); background-size: 320px 220px; background-repeat: repeat; background-position: center;">
    <video #videoElement autoplay playsinline style="visibility: hidden; height: 0px;"></video>
    <ion-card style="width: 90%; max-width: 500px; text-align: center;">
      <ion-card-header>
        <ion-card-title>Login</ion-card-title>
      </ion-card-header>
      <ion-card-content>
        <div style="display: flex; flex-direction: column; align-items: center;">
          <span><br><br></span>
          <ion-input type="text" (ionInput)="onMatriculaInput($event)" fill="outline" placeholder="Exemplo(SP3086420)" [clearInput]="true" style="width: 95%;">
            <div slot="label">Matrícula:</div>
          </ion-input>
          <span><br><br></span>
          <ion-input type="text" (ionInput)="onTokenInput($event)" fill="outline" required [clearInput]="true" placeholder="Exemplo(X89!0aoto00...)" style="width: 95%;">
            <div slot="label">Token:</div>
          </ion-input>
          <span><br></span>
          <ion-button expand="block" id="open-loading" (click)="validarCampos()" class="ion-margin-top" style="width: 95%; color: white; height: 50px;">Entrar</ion-button>
          <ion-loading trigger="open-loading" message="Validando os Dados" duration="2000"></ion-loading>
          <span><br></span>
        </div>
      </ion-card-content>
    </ion-card>
  </div>
</ion-content>
```

#### Descrição

- **`<ion-content>`**: Contém o conteúdo principal da página. Usa um estilo para definir o fundo e centralizar o conteúdo.
- **`<div>`**: Envolve o conteúdo da página, configurando um fundo com uma imagem e centralizando o conteúdo verticalmente e horizontalmente.
  - **`<video>`**: Elemento de vídeo oculto. Não visível e não é usado na interface do usuário, mas pode ser utilizado para funcionalidades futuras como captura de vídeo ou streaming.
- **`<ion-card>`**: Contém o formulário de login.
  - **`<ion-card-header>`**: Cabeçalho do card, que exibe o título "Login".
  - **`<ion-card-content>`**: Conteúdo principal do card, onde estão os campos de entrada e o botão de login.
    - **`<ion-input>`**: Campos de entrada para `matrícula` e `token`.
      - **`type="text"`**: Define o tipo de entrada como texto.
      - **`(ionInput)`**: Event handler para capturar a entrada do usuário.
      - **`fill="outline"`**: Aplica um estilo de contorno aos campos de entrada.
      - **`placeholder`**: Texto de exemplo exibido quando o campo está vazio.
      - **`[clearInput]="true"`**: Adiciona um botão para limpar o campo de entrada.
      - **`style="width: 95%;"`**: Define a largura do campo de entrada.
      - **`<div slot="label">`**: Define o rótulo para o campo de entrada.
    - **`<ion-button>`**: Botão para enviar o formulário.
      - **`(click)="validarCampos()"`**: Event handler que aciona a validação dos campos.
      - **`id="open-loading"`**: Identificador usado para acionar o carregamento.
      - **`style="width: 95%; color: white; height: 50px;"`**: Estilo do botão.
    - **`<ion-loading>`**: Exibe uma animação de carregamento enquanto valida os dados.
      - **`trigger="open-loading"`**: Aciona o carregamento com base no ID do botão.
      - **`message="Validando os Dados"`**: Mensagem exibida durante o carregamento.
      - **`duration="2000"`**: Duração do carregamento em milissegundos.

---

### Variáveis e Funções

- **Variáveis**:
  - **`isDarkTheme`**: Controla o tema atual da aplicação (claro ou escuro).
- **Funções**:
  - **`toggleTheme()`**: Alterna entre os temas claro e escuro.
  - **`onMatriculaInput($event)`**: Manipula a entrada do usuário para o campo `matrícula`.
  - **`onTokenInput($event)`**: Manipula a entrada do usuário para o campo `token`.
  - **`validarCampos()`**: Valida os campos de entrada e realiza a autenticação do usuário.

## Documentação do Typescript -> tab1.page.ts

Aqui está a documentação detalhada para o código TypeScript do componente `Tab1Page`:

---

## Documentação do Código TypeScript

### Importações

```typescript
import { Component, CUSTOM_ELEMENTS_SCHEMA, ViewChild, ElementRef, OnInit, OnDestroy } from '@angular/core';
import { IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/angular/standalone';
import { ExploreContainerComponent } from '../explore-container/explore-container.component';
import { Tab1Service } from '../tab1/tab1.service';
import { ToastController } from '@ionic/angular';
import { DomSanitizer, SafeResourceUrl } from '@angular/platform-browser';
import { Router } from '@angular/router';
import { TabsPage } from '../tabs/tabs.page'; // Importar o TabsPage para acessar o BehaviorSubject
```

#### Descrição

- **Componentes e Decoradores**: Importa `Component`, `CUSTOM_ELEMENTS_SCHEMA`, `ViewChild`, `ElementRef`, `OnInit`, e `OnDestroy` do Angular.
- **Ionic Components**: Importa `IonHeader`, `IonToolbar`, `IonTitle`, e `IonContent` de `@ionic/angular/standalone`.
- **Serviços e Utilitários**:
  - `ExploreContainerComponent`: Componente customizado.
  - `Tab1Service`: Serviço para manipulação de dados do Tab1.
  - `ToastController`: Controlador para exibição de mensagens Toast.
  - `DomSanitizer`, `SafeResourceUrl`: Para sanitização e segurança de URLs.
  - `Router`: Para navegação entre páginas.
  - `TabsPage`: Para acessar e manipular o estado de login.

### Decorador `@Component`

```typescript
@Component({
  selector: 'app-tab1',
  templateUrl: 'tab1.page.html',
  styleUrls: ['tab1.page.scss'],
  standalone: true,
  imports: [IonHeader, IonToolbar, IonTitle, IonContent, ExploreContainerComponent],
  schemas: [CUSTOM_ELEMENTS_SCHEMA],
})
```

#### Descrição

- **`selector`**: Nome do seletor para o componente (`app-tab1`).
- **`templateUrl`**: Caminho para o arquivo de template HTML do componente.
- **`styleUrls`**: Caminho para os arquivos de estilo SCSS do componente.
- **`standalone`**: Indica que o componente é independente e não depende de módulos externos.
- **`imports`**: Importações de módulos e componentes necessários para o componente.
- **`schemas`**: Define os esquemas de elementos customizados a serem reconhecidos no componente.

### Propriedades da Classe

```typescript
export class Tab1Page implements OnInit, OnDestroy {
  @ViewChild('videoElement') videoElement!: ElementRef<HTMLVideoElement>;
  matricula: string = '';
  token: string = '';
  isDarkTheme = false;
  stream: MediaStream | undefined;
  photo: SafeResourceUrl | null = null;
```

#### Descrição

- **`@ViewChild('videoElement')`**: Referência ao elemento `<video>` no template HTML.
- **`matricula`**: Armazena o valor da matrícula fornecida pelo usuário.
- **`token`**: Armazena o valor do token fornecido pelo usuário.
- **`isDarkTheme`**: Indica se o tema atual é escuro.
- **`stream`**: Armazena o fluxo de mídia da câmera.
- **`photo`**: Armazena a URL segura para a foto capturada.

### Construtor

```typescript
constructor(private tab1Service: Tab1Service, private toastController: ToastController, private sanitizer: DomSanitizer, private router: Router, private tabsPage: TabsPage) {}
```

#### Descrição

- **`tab1Service`**: Serviço para validação de matrícula e token.
- **`toastController`**: Controlador para criar e exibir mensagens Toast.
- **`sanitizer`**: Serviço para sanitizar URLs e proteger contra XSS.
- **`router`**: Serviço de roteamento para navegação entre páginas.
- **`tabsPage`**: Instância do `TabsPage` para atualizar o estado de login.

### Métodos do Componente

#### `ngOnInit()`

```typescript
ngOnInit() {
  this.checkLoginStatus();
}
```

- **Descrição**: Método de inicialização do componente. Chama `checkLoginStatus()` para verificar se o usuário já está logado.

#### `ngOnDestroy()`

```typescript
ngOnDestroy() {
  this.stopCamera();
}
```

- **Descrição**: Método chamado quando o componente é destruído. Chama `stopCamera()` para parar a câmera e liberar recursos.

#### `checkLoginStatus()`

```typescript
checkLoginStatus() {
  const sessionToken = sessionStorage.getItem('sessionToken');
  if (sessionToken) {
    this.router.navigate(['/tabs/tab2']);
  }
}
```

- **Descrição**: Verifica se há um token de sessão armazenado. Se sim, redireciona o usuário para a página `/tabs/tab2`.

#### `async presentToast(message: string, color: string)`

```typescript
async presentToast(message: string, color: string) {
  const toast = await this.toastController.create({
    message,
    duration: 2000,
    color
  });
  toast.present();
}
```

- **Descrição**: Cria e exibe uma mensagem Toast com base na `message` e `color` fornecidos.

#### `validar()`

```typescript
validar() {
  const data = {
    registry: this.matricula,
    keycode: this.token
  };

  this.tab1Service.validarMatriculaEToken(data).subscribe(
    async (response) => {
      this.presentToast('Matrícula e token válidos!', 'success');
      console.log('Matrícula e token válidos!', response);

      if (response.token) {
        sessionStorage.setItem('sessionToken', response.token);
        console.log('Token armazenado na sessão:', response.token);
        this.tabsPage.isLoggedIn.next(true); // Atualizar o estado de login
      }

      await this.startCamera();
    },
    (error) => {
      if (error.status === 400) {
        this.presentToast('Matrícula ou token inválidos.', 'danger');
        console.log('Matrícula ou token inválidos.', error.error);
      } else {
        this.presentToast('Erro ao validar matrícula e token.', 'danger');
        console.error('Erro ao fazer a requisição:', error);
      }
    }
  );
}
```

- **Descrição**: Valida a matrícula e o token fornecidos usando o `Tab1Service`. Se a validação for bem-sucedida, armazena o token na sessão e chama `startCamera()`. Caso contrário, exibe uma mensagem de erro.

#### `async startCamera()`

```typescript
async startCamera() {
  try {
    this.stopCamera();
    this.stream = await navigator.mediaDevices.getUserMedia({ video: true });
    const videoElement = this.videoElement.nativeElement;
    videoElement.srcObject = this.stream;
    this.router.navigate(['/tabs/tab2']);
  } catch (err) {
    console.error('Erro ao acessar a câmera:', err);
    this.presentToast('Erro ao acessar a câmera.', 'danger');
    this.tab1Service.deleteAllSessions();
  }
}
```

- **Descrição**: Inicia a câmera e exibe o fluxo de vídeo no elemento `<video>`. Em caso de erro, exibe uma mensagem de erro e exclui todas as sessões.

#### `stopCamera()`

```typescript
stopCamera() {
  if (this.stream) {
    this.stream.getTracks().forEach(track => track.stop());
    this.stream = undefined;
  }
}
```

- **Descrição**: Para o fluxo de mídia da câmera, se estiver em execução, e limpa a referência ao `stream`.

#### `toggleTheme()`

```typescript
toggleTheme() {
  this.isDarkTheme = !this.isDarkTheme;
  document.body.classList.toggle('dark', this.isDarkTheme);
}
```

- **Descrição**: Alterna o tema da aplicação entre claro e escuro e aplica a classe correspondente ao corpo do documento.

#### `validarCampos()`

```typescript
validarCampos() {
  console.log('Matrícula digitada:', this.matricula);
  console.log('Token digitado:', this.token);

  if (this.matricula && this.token) {
    this.validar();
  } else {
    this.presentToast('Preencha todos os campos.', 'warning');
    console.log('Preencha todos os campos.');
  }
}
```

- **Descrição**: Valida se os campos de matrícula e token foram preenchidos. Se sim, chama `validar()`. Caso contrário, exibe uma mensagem de aviso.

#### `onMatriculaInput(event: any)`

```typescript
onMatriculaInput(event: any) {
  this.matricula = event.target.value;
}
```

- **Descrição**: Atualiza a propriedade `matricula` com o valor inserido pelo usuário no campo de matrícula.

#### `onTokenInput(event: any)`

```typescript
onTokenInput(event: any) {
  this.token = event.target.value;
}
```

- **Descrição**: Atualiza a propriedade `token` com o valor inserido pelo usuário no campo de token.

---

## Documentação do tab1.service.ts

Aqui está a documentação detalhada para o código TypeScript do serviço `Tab1Service`:

---

### Importações

```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';
```

#### Descrição

- **`Injectable`**: Decorador que marca a classe como um serviço que pode ser injetado em outros componentes ou serviços.
- **`HttpClient`**: Serviço para realizar operações HTTP.
- **`HttpErrorResponse`**: Tipo para representar respostas de erro HTTP.
- **`Observable`**: Tipo de objeto que representa um fluxo de dados assíncronos.
- **`throwError`**: Função para criar um Observable que emite um erro.
- **`catchError`**: Operador RxJS para capturar erros e retornar um Observable de erro.

### Decorador `@Injectable`

```typescript
@Injectable({
  providedIn: 'root',
})
```

#### Descrição

- **`providedIn`**: Define que o serviço será fornecido na raiz do aplicativo, tornando-o disponível em toda a aplicação.

### Classe `Tab1Service`

```typescript
export class Tab1Service {
  private apiUrl = 'http://192.168.15.13:8081/auth/session'; // Substitua pela sua URL de API

  constructor(private http: HttpClient) {}
```

#### Propriedades

- **`apiUrl`**: URL base da API para enviar requisições relacionadas à sessão de autenticação. Deve ser substituída pela URL da API real.

#### Construtor

```typescript
constructor(private http: HttpClient) {}
```

- **`http`**: Instância do `HttpClient` para realizar requisições HTTP.

### Métodos da Classe

#### `validarMatriculaEToken(data: { registry: string; keycode: string }): Observable<any>`

```typescript
validarMatriculaEToken(data: { registry: string; keycode: string }): Observable<any> {
  console.log(data);

  // Printar o tamanho de cada string
  console.log('Tamanho da matrícula:', data.registry.length);
  console.log('Tamanho do token:', data.keycode.length);
  
  return this.http.post<any>(this.apiUrl, data).pipe(
    catchError((error: HttpErrorResponse) => {
      console.error('Erro ao validar matrícula e token:', error);
      return throwError(() => new Error('Erro ao validar matrícula e token'));
    })
  );
}
```

- **Descrição**: Envia uma requisição POST para a API com a matrícula e o token fornecidos. O método imprime no console o tamanho da matrícula e do token. Em caso de erro, captura e exibe o erro, retornando um Observable de erro.
- **Parâmetros**:
  - `data`: Objeto contendo `registry` (matrícula) e `keycode` (token) como strings.
- **Retorno**: Um Observable que emite a resposta da API ou um erro se a requisição falhar.

#### `deleteAllSessions()`

```typescript
public deleteAllSessions() {
  // Remover o token e outras sessões armazenadas no sessionStorage
  sessionStorage.removeItem('sessionToken');
  sessionStorage.clear();

  // Fazer a requisição DELETE para o servidor
  return this.http.delete('http://192.168.15.13:8081/auth/session', { responseType: 'text' })
    .pipe(
      catchError((error: HttpErrorResponse) => {
        console.error('Erro ao deletar as sessões no servidor:', error);
        return throwError(() => new Error('Erro ao deletar as sessões no servidor'));
      })
    );
}
```

- **Descrição**: Remove o token e outras sessões armazenadas no `sessionStorage`. Em seguida, envia uma requisição DELETE para o servidor para remover as sessões no lado do servidor. Captura e exibe qualquer erro que ocorra, retornando um Observable de erro.
- **Retorno**: Um Observable que emite a resposta do servidor ou um erro se a requisição falhar.

---
