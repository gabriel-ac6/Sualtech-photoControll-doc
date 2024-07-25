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

Se precisar de mais detalhes ou tiver dúvidas adicionais, é só avisar!
