#### Estrutura de Tabs2 (tela de tirar foto) no Ionic


### Documentação do Código


#### Métodos do Componente

---

## Estrutura HTML e Lógica

### Cabeçalho (`ion-header`)

#### Estrutura
```html
<ion-header [translucent]="true">
  <ion-toolbar>
    <ion-title style="text-align: center;">
      Photo Controll
    </ion-title>
    <ion-buttons slot="end">
      <ion-button (click)="toggleTheme()">
        <ion-icon [name]="isDarkTheme ? 'sunny-outline' : 'moon-outline'"></ion-icon>
      </ion-button>
    </ion-buttons>
    <ion-buttons slot="start">
      <span style=" background-color: black; width: 63px; border-radius: 4pt;">
        <ion-img src="../../assets/logo-sualtech.png" alt="Logo"></ion-img>
      </span>
    </ion-buttons>
  </ion-toolbar>
</ion-header>
```

#### Descrição

- **`<ion-header>`**: Componente que define o cabeçalho da página. O atributo `[translucent]="true"` faz com que o cabeçalho seja semi-transparente.
- **`<ion-toolbar>`**: Contém o conteúdo do cabeçalho.
- **`<ion-title>`**: Exibe o título "Photo Controll" centralizado.
- **`<ion-buttons slot="end">`**: Adiciona um botão no final do cabeçalho.
  - **Botão de Tema**: Alterna entre o modo claro e escuro. Usa um ícone (`sunny-outline` ou `moon-outline`) dependendo do estado `isDarkTheme`.
- **`<ion-buttons slot="start">`**: Adiciona um botão no início do cabeçalho.
  - **Logo**: Exibe uma imagem (logo da empresa) com um fundo preto.

### Conteúdo (`ion-content`)

#### Estrutura
```html
<ion-content>
  <div style=" justify-content: center; align-items: center; height: 100%; background-image: url('../assets/teste4.png'); background-size: 320px 220px; background-repeat: repeat; background-position: center;">
    
    <!-- Se as instruções não são mostradas -->
    <div *ngIf="!showInstructions">
      <div style="display: flex; justify-content: center; align-items: center; height: 100%; position: relative;">
        <ion-card>
          <ion-card-header style="background-color: black;">
            <ion-card-title style="text-align: center; color: white;">Tire sua foto</ion-card-title>
          </ion-card-header>
          <ion-card-content>
            <br><br>
            <form style="display: flex; flex-direction: column; align-items: center;">
              <div id="cameraPreview" style="position: relative; width: 100%; height: 100%;">
                <video #videoElement *ngIf="!isPhotoTaken" autoplay playsinline style="width: 100%; height: 100%;"></video>
                <canvas #canvasElement style="display: none;"></canvas>
                <img src="../../assets/icon/user.png" alt="Silhueta" *ngIf="!isPhotoTaken" style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 87%; height: auto; pointer-events: none;">
              </div>

              <!-- Botões para voltar e tirar foto -->
              <div *ngIf="!isPhotoTaken" style="display: flex; gap: 10px; margin-top: 10px;">
                <ion-button expand="block" (click)="toggleVisibility()" style="color: white; width: 190px;">
                  Voltar para as Instruções<span style="width: 10px;"></span><ion-icon name="arrow-back-outline"></ion-icon>
                </ion-button>
                <ion-button expand="block" (click)="takePicture()" style="color: white; width: 190px;">
                  Tirar Foto<span style="width: 10px;"></span><ion-icon name="camera"></ion-icon>
                </ion-button>
              </div>

              <!-- Pré-visualização da foto e botões de enviar e cancelar -->
              <div *ngIf="photo && isPhotoTaken" style="display: flex; flex-direction: column; align-items: center;">
                <img [src]="photo" style="margin-top: 10px; max-width: 100%; height: auto;">
                <div style="display: flex; gap: 10px; margin-top: 10px;">
                  <ion-button expand="block" (click)="sendPhoto()" style="color: white; width: 190px;">
                    Enviar Foto<span style="width: 10px;"></span><ion-icon name="checkmark-done-outline"></ion-icon>
                  </ion-button>
                  <ion-button expand="block" (click)="cancelPhoto()" style="color: white; width: 190px;">
                    Cancelar<span style="width: 10px;"></span><ion-icon name="close-outline"></ion-icon>
                  </ion-button>
                </div>
              </div>

              <br>
            </form>
          </ion-card-content>
        </ion-card>
      </div>
    </div>
    
    <!-- Se as instruções são mostradas -->
    <div *ngIf="showInstructions">
      <div style=" display: flex; justify-content: center; align-items: center; height: auto;">
        <swiper-content style="width: 100%; max-width: 800px;">
          <swiper-container 
            #swiperEx
            initial-slide="0"
            (slidechange)="onSlideChange()"
            [pagination]="true"
            allowTouchMove="false"
            [allowSlidePrev]="!isPrevButtonDisabled"
            [allowSlideNext]="!isNextButtonDisabled"
            [allow-touch-move]="!isSlideDraggingDisabled">
            
            <!-- Slides das instruções -->
            <swiper-slide>
              <ion-card class="custom-card" style="margin-left: 10%;">
                <ion-card-header>
                  <ion-card-title style="text-align: center; color: white;">Manter rosto centralizado</ion-card-title>
                </ion-card-header>
                <ion-card-content>
                  <div>
                    <br><br>
                    <span style="display: flex; justify-content: center; align-items: center;">
                      <img src="../../assets/icon/face-scan.gif" width="40%" alt="GIF Aleatório">
                    </span>
                    <br><br>
                    <span style="display: flex; flex-direction: column; align-items: center;">
                      <h3 style="font-weight: bold; font-size: 14pt; text-align: center;">Comportamento correto para o rosto:</h3>
                      <ul>
                        <li style="font-weight: bold;">Rosto centralizado, olhando para câmera</li>
                        <li style="font-weight: bold;">Cabeça reta e alinhada</li>
                      </ul>
                    </span>
                    <br>
                  </div>
                  <div style="text-align: center;">
                    <ion-button (click)="onSlidePrev()" [disabled]="isPrevButtonDisabled" id="prevButton" style="color: gray; width: 100px;"> Voltar</ion-button>
                    <ion-button (click)="onSlideNext()" [disabled]="isNextButtonDisabled" style="color: white; width: 130px;"> Próximo</ion-button>
                    <br><br>
                  </div>
                  <div class="progress-bar" [style.width]="progressBarWidth"></div>
                </ion-card-content>
              </ion-card>
            </swiper-slide>

            <swiper-slide>
              <ion-card class="custom-card" style="margin-left: 10%;">
                <ion-card-header>
                  <ion-card-title style="text-align: center; color: white;">Procurar uma parede branca</ion-card-title>
                </ion-card-header>
                <ion-card-content>
                  <br><br>
                  <span style="display: flex; justify-content: center; align-items: center;">
                    <img src="../../assets/icon/wall.gif" width="40%" alt="GIF Aleatório">
                  </span>
                  <br><br>
                  <span style="display: flex; flex-direction: column; align-items: center;">
                    <h3 style="font-weight: bold; font-size: 14pt; text-align: center;">O correto para o fundo da imagem:</h3>
                    <ul>
                      <li style="font-weight: bold;">Deve ser em um lugar claro</li>
                      <li style="font-weight: bold;">Estar em um fundo branco</li>
                    </ul>
                  </span>   
                  <br>
                  <div style="text-align: center;">
                    <ion-button (click)="onSlidePrev()" [disabled]="isPrevButtonDisabled" style="color: white; width: 130px;"> Voltar</ion-button>
                    <ion-button (click)="onSlideNext()" [disabled]="isNextButtonDisabled" style="color: white; width: 130px;"> Próximo </ion-button>
                    <br><br>
                  </div>
                  <div class="progress-bar" [style.width]="progressBarWidth"></div>
                </ion-card-content>
              </ion-card>
            </swiper-slide>

            <swiper-slide>
              <ion-card class="custom-card" style="margin-left: 10%;">
               

 <ion-card-header>
                  <ion-card-title style="text-align: center; color: white;">Garantir boa iluminação</ion-card-title>
                </ion-card-header>
                <ion-card-content>
                  <br><br>
                  <span style="display: flex; justify-content: center; align-items: center;">
                    <img src="../../assets/icon/flash.gif" width="40%" alt="GIF Aleatório">
                  </span>
                  <br><br>
                  <span style="display: flex; flex-direction: column; align-items: center;">
                    <h3 style="font-weight: bold; font-size: 14pt; text-align: center;">A iluminação deve:</h3>
                    <ul>
                      <li style="font-weight: bold;">Ser bem distribuída</li>
                      <li style="font-weight: bold;">Não deixar sombras no rosto</li>
                    </ul>
                  </span>   
                  <br>
                  <div style="text-align: center;">
                    <ion-button (click)="onSlidePrev()" [disabled]="isPrevButtonDisabled" style="color: white; width: 130px;"> Voltar</ion-button>
                    <ion-button (click)="onSlideNext()" [disabled]="isNextButtonDisabled" style="color: white; width: 130px;"> Próximo</ion-button>
                    <br><br>
                  </div>
                  <div class="progress-bar" [style.width]="progressBarWidth"></div>
                </ion-card-content>
              </ion-card>
            </swiper-slide>

            <swiper-slide>
              <ion-card class="custom-card" style="margin-left: 10%;">
                <ion-card-header>
                  <ion-card-title style="text-align: center; color: white;">Imagem deve ter um rosto</ion-card-title>
                </ion-card-header>
                <ion-card-content>
                  <br><br>
                  <span style="display: flex; justify-content: center; align-items: center;">
                    <img src="../../assets/icon/single-face.gif" width="40%" alt="GIF Aleatório">
                  </span>
                  <br><br>
                  <span style="display: flex; flex-direction: column; align-items: center;">
                    <h3 style="font-weight: bold; font-size: 14pt; text-align: center;">Certifique-se de que:</h3>
                    <ul>
                      <li style="font-weight: bold;">Apenas um rosto está visível</li>
                      <li style="font-weight: bold;">Não há outras pessoas na imagem</li>
                    </ul>
                  </span>
                  <br>
                  <div style="text-align: center;">
                    <ion-button (click)="onSlidePrev()" [disabled]="isPrevButtonDisabled" style="color: white; width: 130px;"> Voltar</ion-button>
                    <ion-button (click)="onSlideNext()" [disabled]="isNextButtonDisabled" style="color: white; width: 130px;"> Próximo</ion-button>
                    <br><br>
                  </div>
                  <div class="progress-bar" [style.width]="progressBarWidth"></div>
                </ion-card-content>
              </ion-card>
            </swiper-slide>

            <swiper-slide>
              <ion-card class="custom-card" style="margin-left: 10%;">
                <ion-card-header>
                  <ion-card-title style="text-align: center; color: white;">Aceite os termos de uso</ion-card-title>
                </ion-card-header>
                <ion-card-content>
                  <br><br>
                  <span style="display: flex; justify-content: center; align-items: center;">
                    <img src="../../assets/icon/terms.gif" width="40%" alt="GIF Aleatório">
                  </span>
                  <br><br>
                  <span style="display: flex; flex-direction: column; align-items: center;">
                    <h3 style="font-weight: bold; font-size: 14pt; text-align: center;">Para continuar:</h3>
                    <ul>
                      <li style="font-weight: bold;">Leia e aceite os termos de uso</li>
                    </ul>
                  </span>
                  <br>
                  <div style="text-align: center;">
                    <ion-button (click)="onSlidePrev()" [disabled]="isPrevButtonDisabled" style="color: white; width: 130px;"> Voltar</ion-button>
                    <ion-button (click)="onSlideNext()" [disabled]="isNextButtonDisabled" style="color: white; width: 130px;"> Próximo</ion-button>
                    <br><br>
                  </div>
                  <div class="progress-bar" [style.width]="progressBarWidth"></div>
                </ion-card-content>
              </ion-card>
            </swiper-slide>
          </swiper-container>
        </swiper-content>
      </div>
    </div>
  </div>
</ion-content>
```

#### Descrição

- **`<ion-content>`**: Componente principal para o conteúdo da página.
- **Background**: Define uma imagem de fundo para a página.
  
- **Se `!showInstructions` (Instruções Ocultas)**:
  - **Card de Captura**: Exibe um card com opções para capturar uma foto.
  - **`#cameraPreview`**: Contém um vídeo ou canvas para exibir a visualização da câmera.
    - **`<video>`**: Elemento de vídeo que mostra a visualização ao vivo da câmera.
    - **`<canvas>`**: Elemento usado para desenhar a imagem capturada.
    - **`<img>`**: Exibe uma silhueta enquanto a foto não é tirada.
  - **Botões**:
    - **Voltar para as Instruções**: Volta para o tutorial.
    - **Tirar Foto**: Captura a foto.

- **Se `showInstructions` (Instruções Visíveis)**:
  - **Swiper Container**: Exibe um container de slides com instruções.
  - **Slides**:
    - **Slide 1**: Instruções sobre manter o rosto centralizado.
    - **Slide 2**: Instruções para procurar uma parede branca.
    - **Slide 3**: Instruções para garantir boa iluminação.
    - **Slide 4**: Instruções para garantir que apenas um rosto esteja visível.
    - **Slide 5**: Instruções para aceitar os termos de uso.
  - **Botões de Navegação**:
    - **Voltar**: Navega para o slide anterior.
    - **Próximo**: Navega para o próximo slide.

---

### Lógica TypeScript

#### Funções e Variáveis
- **`toggleVisibility()`**: Alterna a visibilidade entre a captura de foto e as instruções.
- **`takePicture()`**: Captura uma foto usando a câmera.
- **`sendPhoto()`**: Envia a foto capturada para o servidor.
- **`cancelPhoto()`**: Cancela a captura da foto e volta à visualização anterior.
- **`onSlideChange()`**: Atualiza o estado dos botões de navegação com base no slide atual.
- **`onSlidePrev()`**: Navega para o slide anterior.
- **`onSlideNext()`**: Navega para o próximo slide.

#### Estado e Variáveis
- **`showInstructions`**: Controla se as instruções são exibidas.
- **`isPhotoTaken`**: Controla se a foto foi tirada.
- **`photo`**: Armazena a imagem capturada.
- **`progressBarWidth`**: Controla a largura da barra de progresso com base no progresso dos slides.
- **`isPrevButtonDisabled`**: Habilita ou desabilita o botão "Voltar" dependendo do slide atual.
- **`isNextButtonDisabled`**: Habilita ou desabilita o botão "Próximo" dependendo do slide atual.
- **`isSlideDraggingDisabled`**: Controla se o deslizar dos slides é permitido.

---

### Estilos e Design

- **ProgressBar**: A largura da barra de progresso é ajustada dinamicamente para refletir o progresso através dos slides.
- **Botões**: Ajustados para diferentes estados, como habilitados e desabilitados, com cores e ícones para melhor acessibilidade e feedback visual.
- **Cards**: Usam cores e estilos específicos para destacar as instruções e melhorar a legibilidade.

Se precisar de mais detalhes sobre uma seção específica ou se tiver alguma dúvida adicional, é só avisar!


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
