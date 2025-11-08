# Ola-app
Este projeto implementa um pipeline completo de CI/CD (Integração Contínua e Entrega Contínua) para uma aplicação FastAPI, 
demonstrando práticas modernas de DevOps com GitHub Actions, Docker, e 
ArgoCD para deploy automatizado em Kubernetes.

A solução automatiza todo o ciclo de desenvolvimento - desde o commit do código até o deploy em produção - garantindo entregas rápidas, seguras e consistentes.

##  :hammer:Tecnologias Utilizadas
![Github Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)
![Kubernetes](https://img.shields.io/badge/kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![Rancher Desktop](https://img.shields.io/badge/Rancher_Desktop-1F6BFF?style=for-the-badge&logo=rancher&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF4836?style=for-the-badge&logo=argo&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![Docker Hub](https://img.shields.io/badge/Docker_Hub-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Visual Studio Code](https://img.shields.io/badge/Visual_Studio_Code-0078D4?style=for-the-badge&logo=visual%20studio%20code&logoColor=white)

## ⚙️ Pré-requisitos
* GitHub Account com repositório público

* Docker Hub com token de acesso

* Rancher Desktop com Kubernetes habilitado

* kubectl configurado e funcionando

* ArgoCD instalado no cluster local

* Git instalado localmente

# Etapa 1 Criando os Repositórios no Github

Para execução desse projeto, vamos precisar de 2 repositórios, o repositório A vai conter nossa aplicação que vai ser criada com auxilio da ferramenta FastAPI, o Dockerfile onde iremos fazer a conteinerização que é o processo de agrupar uma aplicação e todas as suas dependências (bibliotecas, arquivos de configuração) em um único pacote leve e isolado, chamado contêiner. O repositório B que vai conter os manifestos do service e deployment para criarmos a aplicação com o ArgoCD.

## 1.1 Criar o repositório A (ola-app)

Para criar o repositório, entre na sua conta do github, na página inicial.
* Clique em **repositories** que está localizado no menu superior .
    
<img width="614" height="136" alt="image" src="https://github.com/user-attachments/assets/0b5820ae-80cf-463e-bfab-3ffd34c143bf" />

*  Clique no botão verde escrito **New**
<img width="555" height="157" alt="image" src="https://github.com/user-attachments/assets/6b3c2c8a-bbc0-4282-bbcf-dd55249e4a7c" />

* Clicando em **New** vai entrar na tela da imagem abaixo

<img width="940" height="796" alt="Captura de tela 2025-11-07 100429" src="https://github.com/user-attachments/assets/168dab65-c710-45db-8817-2675f18dafba" />

* Repository name*: Colocar o nome do repositório no meu caso **ola-app**.
* Description: Colocar uma pequena descrição sobre o projeto.
* Choose visibility*: Opção para escolher a visibilidade do repositório deixaremos **Public**.
* Add README : **on**.
* Add .gitignore: **No .gitignore**.
* Add license - **No license**.
* Para finalizar clique em **Create Repository**.

Faça o mesmo processo para o repositório B alterando:
* Repository name: ```ola-manifest```.
* Add README: **off**

Link para o repositório: https://github.com/italolinux/ola-manifest

# Etapa 2 Criando aplicação e Dockerfile
Usando o Visual Studio Code, vamos criar os arquivos.
* **requirements.txt**
* **main.py**
* **Dockerfile**

## Etapa 2.1 Fazendo clone do repositório
Com os repositórios criados, vamos clonar nosso repositorio A.
```
git clone https://github.com/YOUR-USERNAME/ola-app 
cd ola-app
```
Feito isso podemos criar nossos arquivos.
## 2.1 Código da Aplicação 
Vamos criar um arquivo chamado ```main.py``` onde está o código que cria uma API web simples usando **FastAPI** que, quando acessada na rota principal (/), retorna uma mensagem JSON {"message": "Hello World from FastAPI"}. O FastAPI inicializa uma aplicação que fica escutando requisições HTTP e responde automaticamente na rota configurada, sendo ideal para criar endpoints de API de forma rápida e eficiente.

## 2.2 Dependências do Projeto
O arquivo ```requirements.txt``` tem como propósito listar e gerenciar as dependências do projeto, especificando que para executar a aplicação FastAPI são necessárias as bibliotecas **fastapi** para criar a **API web** e **uvicorn[standard]** com todos os recursos extras para servir a aplicação em ambiente de produção. Este arquivo permite que outros desenvolvedores (ou ambientes de CI/CD) instalem automaticamente as versões corretas das dependências usando o comando ```pip install -r requirements.txt``` , garantindo consistência e reprodução do ambiente de execução.

## 2.3 Dockerfile
``` Dockerfile
FROM python:3.11-slim
WORKDIR /hello-app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```
Este Dockerfile tem como propósito criar uma imagem Docker containerizada da aplicação **FastAPI**, partindo da **imagem base Python 3.11-slim**, copiando e instalando as dependências do projeto definidas no **requirements.txt**, transferindo todo o código da aplicação para o container e finalmente executando o **servidor Uvicorn na porta 80**, permitindo que a API seja acessível externamente e pronta para deployment em qualquer ambiente que suporte containers Docker.

## 2.4 Fazendo o push para o repositório A
Com todos os arquivos já criados, envie para o repositório remoto no Github usando os seguintes comandos:

```
git add . #adiciona arquivos novos ou alterados do seu diretório de trabalho à área de preparação do Git.
git commit -m "Adicionando o main.py, requirements.txt e dockerfile"
git push origin main
```
* **git add .** : adiciona arquivos novos ou alterados do seu diretório de trabalho à área de preparação do Git.
* **git commit**: os commits são os blocos de construção dos "pontos de salvamento" no sistema de controle de versões do Git.
* **git push origin main** : fazer upload do conteúdo do repositório local para um repositório remoto e branch especificados.

# Etapa 3 Criar o GitHub Actions (CI/CD)
Com a base da aplicação já implementada, o próximo passo consiste em automatizar o processo de build e preparação do deploy através da implementação do GitHub Actions, que atuará como servidor de Integração Contínua (CI), onde será criado um workflow automatizado que, a cada push na branch principal, constrói uma nova imagem Docker, realiza seu push para o registry e abre automaticamente um Pull Request para atualização da versão no repositório de manifestos, garantindo que toda alteração no código seja adequadamente empacotada e preparada para deployment seguindo as práticas de CI/CD.

**obs**: O Docker Hub será utilizado como Container Registry para hospedar as imagens Docker da aplicação. Caso você ainda não possua uma conta, é necessário criar uma gratuitamente no site oficial. Após realizar o login, registre seu nome de usuário, pois essa informação será essencial para as etapas subsequentes do projeto.

[Acessar Docker hub](https://hub.docker.com)

## 3.1 Gerando Token de Acesso no Docker hub
Por questões de segurança, é fundamental evitar a exposição da senha principal da conta. Como alternativa, deve-se gerar um Token de Acesso (Access Token) que concederá permissões específicas ao GitHub Actions para se conectar à conta do Docker Hub. O tutorial a seguir demonstrará o procedimento adequado para criar e configurar este token de forma segura.

* Entrando no docker hub clique no icone da sua conta localizado no canto superior do lado direito, com isso abrirá uma pequena janela.
<img width="405" height="347" alt="image" src="https://github.com/user-attachments/assets/18796526-29fa-41fa-8e0a-f8a5c4af9060" />
* Selecione **Account Settings**.

* No menu ao lado esquerdo selecione **Personal access tokens**.

* Depois selecione **generate new token**.

<img width="803" height="560" alt="image" src="https://github.com/user-attachments/assets/d8b1afe9-05d3-49aa-b87c-57db2fef65d5" />

* Access token description: onde você define um nome para seu token, como ``` hello-app-key ```.

* Expiration date: Onde se define uma data para expirar o token.

* Access permissions: **Read, Write, Delete**. (níveis de autorização que você concede a um token de acesso)

* Para finalizar clique em **Generate**.

### ⚠️ Importante:

* Anote o token gerado - Ele só será mostrado uma vez

* Mantenha o token seguro - Não o exponha publicamente

Esta configuração permitirá que o GitHub Actions faça push das imagens Docker para seu repositório no Docker Hub de forma segura!

## 3.2 Criando o par de chaves para o acesso entre os repositórios
Para que o workflow no repositório ```ola-app``` tenha permissão para escrever (git push) no repositório ```ola-manifest```, é necessário configurar um par de chaves SSH. Utilize o comando abaixo para gerar as chaves:
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
Se estiver usando um sistema herdado que não dá suporte ao algoritmo Ed25519, use:
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
* Escreva o nome da chave, (ex: github-ola-app).
* Sem senha (passphrase), pois o GitHub Actions não sabe digitá-la.

Este comando criará:

* Uma chave privada **(github-ola-app)** - que será armazenada como secret no GitHub

* Uma chave pública **(github-ola-app.pub)** - que será adicionada às deploy keys do repositório hello-manifests

Essa configuração permitirá a autenticação segura entre os repositórios durante a automação do CI/CD, Mas não se preocupe agora, vou ensinar a fazer o deploy e o secrets nas etapas seguintes.

## 3.3 Personal Access Token (PAT)
Enquanto a chave SSH é utilizada para operações Git, a criação de Pull Requests opera através da API do GitHub, exigindo um Personal Access Token (PAT). Para gerar este token, siga os passos abaixo:
* Acesse as configurações do GitHub: No seu perfil, navegue para Settings > Developer settings > Personal access tokens > Tokens (classic)

*Crie um novo token: Clique em **Generate new Token**.

Configure o token:

* **Note:** Atribua um nome descritivo, como ```Automacao-pull-request```

* **Expiration:** Defina uma data de validade adequada

* **Select scopes:** Marque a caixa de seleção principal repo para conceder permissões de repositório

## 3.4 Criando os Secrets no github
Para garantir que o GitHub Actions possa se conectar ao Docker Hub de forma segura, sem expor credenciais diretamente no código, utilizamos os **"Secrets"** do repositório. Eles funcionam como um cofre de senhas para a automação. Siga estas etapas:

* Acesse as configurações do repositório da aplicação no GitHub.(no meu caso **ola-app**)

* Navegue até a seção **"Secrets and variables"** > **"Actions"**.

* Clique em **"New repository secret"** para adicionar cada credencial necessária.

Configure os seguintes secrets:

* ```DOCKER_USERNAME```: Seu nome de usuário do Docker Hub.

* ```DOCKER_PASSWORD```: Token de acesso do Docker Hub, **criado na etapa 3.1**.

* ```SSH_PRIVATE_KEY```: Chave SSH privada para acesso ao repositório de manifests, no meu caso ```github-ola-app``` criado na **etapa 3.2**.

* ```TOKEN_PR```: Cole o **Personal Access Token** gerado.

<img width="1785" height="502" alt="image" src="https://github.com/user-attachments/assets/2eafdcac-209c-4d91-ad89-ae3ddf3e4fdb" />

Esta abordagem mantém as informações sensíveis protegidas enquanto permite que o workflow as utilize durante a execução das pipelines.


## 3.5 Deploy key no Github
A chave pública deve ser adicionada ao repositório que receberá o acesso, neste caso, o ```ola-manifest```.

Siga estas etapas para configurar:

* Acesse o repositório ```ola-manifest``` no GitHub.

* Navegue até **Settings** > **Deploy keys** e clique em **Add deploy key**.

Preencha os campos:

* Title: Atribua um nome identificador, como ```ola-app-workflow```.

* Key: Cole o conteúdo completo do arquivo da chave pública (ex: ```github-ola-app.pub```)

* Marque a opção **"Allow write access"** para permitir que a chave realize operações de push

Esta configuração habilita a autenticação segura via SSH, permitindo que o workflow do ```ola-app``` faça push de alterações automaticamente no repositório de manifestos.

## 3.6 Criando o Workflow
Com todas as permissões e segredos devidamente configurados, a etapa final consiste em criar o arquivo de workflow que orquestrará toda a automação de CI/CD. Este arquivo atuará como o cérebro da pipeline.

Para implementá-lo:

* Crie a estrutura de pastas .github/workflows/ no repositório da aplicação ```ola-app```.

* Dentro desta pasta, crie o arquivo ```gitActions.yaml``` com o conteúdo disponibilizado abaixo:
```
name: CI/CD - Build & Update Manifests

on:
  push:
    branches: [ "main" ]
    
    paths-ignore:
      -  'README.md'

permissions:
  contents: write
  pull-requests: write

jobs:
  build-and-update:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Login Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/hello-app:${{ github.sha }}

      - name: Setup SSH
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_ed25519
          chmod 600 ~/.ssh/id_ed25519
          ssh-keyscan github.com >> ~/.ssh/known_hosts
  update-manifest:
     needs: build-and-update
     runs-on: ubuntu-latest
    
     steps:
      - name: Checkout manifests repository
        uses: actions/checkout@v4
        with:
          repository: italolinux/ola-manifest
          ssh-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: Update image tag
        run: |
          sed -i "s| ${{ secrets.DOCKER_USERNAME }}/hello-app:.*| ${{ secrets.DOCKER_USERNAME }}/hello-app:${{ github.sha }}|g" deployment.yaml

      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v6
        with:
          token: ${{ secrets.TOKEN_PR }}
          commit-message: "ci: update image to ${{ github.sha }}"
          title: "Atualizar imagem para ${{ github.sha }}"
          body: "Atualização automática da imagem Docker para `${{ github.sha }}`"
          branch: "update-image-${{ github.sha }}"
          base: "main"
```
Com esta configuração, você terá uma pipeline de CI totalmente funcional que será acionada automaticamente a cada alteração no código da aplicação, garantindo um processo contínuo de integração e entrega.

O workflow estará pronto para ser executado automaticamente, quando ocorrer o **push**.

Se Workflow estiver certo, a pipeline gerada será essa:
<img width="1548" height="627" alt="image" src="https://github.com/user-attachments/assets/b14bfeeb-8569-43c9-ab6e-2c92cb63cbd9" />

A imagem criado no Docker Hub, será essa:

<img width="1877" height="538" alt="image" src="https://github.com/user-attachments/assets/299da066-f28e-4166-8be4-8e6ac0be2514" />

Abaixo mostra a evidência do pull request para o ```ola-manifest```:

<img width="1850" height="829" alt="Captura de tela 2025-11-08 124027" src="https://github.com/user-attachments/assets/daac4ac4-8452-4646-917e-222e442bdc5c" />












