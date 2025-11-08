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

# Etapa 4 Criação dos Manifestos
Com a pipeline de CI operando corretamente, o próximo passo é definir a execução da aplicação dentro do cluster Kubernetes. Para isso, é necessário criar os arquivos de manifesto no repositório ```ola-manifest```. Estes arquivos servirão como a "fonte da verdade" que o ArgoCD utilizará para gerenciar e sincronizar o estado da aplicação no cluster, garantindo que a infraestrutura sempre reflita o que está definido no controle de versão.

## 4.1 Clone do repositório de Manifestos.
Para editar e criar os arquivos de manifestos no repositório ```ola-manifest```, precisamos clonar ele, para o visual studio.
Em um terminal bash no visual studio:
``` bash
git clone https://github.com/SEU_USUARIO/ola-manifest.git
cd ola-manifest
```
## 4.2 Manifesto de deployment
O **Deployment** é um objeto do Kubernetes responsável por gerenciar a implantação e escalabilidade da aplicação, assegurando que um número específico de réplicas (pods) permaneça em execução conforme definido.

Para implementá-lo, criaremos o arquivo ```deployment.yaml``` com o seguinte conteúdo de configuração:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-app
  labels:
    app: hello-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-app
  template:
    metadata:
      labels:
        app: hello-app
    spec:
      containers:
        - name: hello-app
          image: DOCKER_USERNAME/hello-app:e737866e28422de83fc3f9b8a79aa912a6d2a293
          ports:
            - containerPort: 80
```
Lembre-se de substituir **DOCKER_USERNAME** pelo seu nome de usuário do Docker Hub.

## 4.3 Manifesto de Service
O **Service** é um objeto do Kubernetes que expõe a aplicação como um serviço de rede, criando um ponto de acesso estável e permanente para os pods gerenciados pelo Deployment.

Para implementá-lo, criaremos o arquivo ```service.yaml``` com a seguinte configuração:
```
apiVersion: v1
kind: Service
metadata:
  name: hello-app
spec:
  selector:
    app: hello-app
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
## 4.4 Enviar os arquivos para o Github
Após a criação dos dois arquivos de manifesto, eles devem ser commitados e enviados via push para a branch main do repositório ```ola-manifest```, estabelecendo assim a configuração base que será utilizada pelo ArgoCD para o deploy da aplicação no cluster Kubernetes.
```
git add .
git commit -m "Adicionado Manifestos"
git push origin main
```
# Etapa 5 Criar Aplicação no ArgoCD.
O ArgoCD é uma ferramenta open-source essencial para GitOps e Continuous Delivery, especializando-se exclusivamente na etapa de deployment (CD) em Kubernetes. Como um operador nativo do Kubernetes, ele estende a plataforma através de Custom Resources e Controllers para gerenciar aplicações de forma declarativa, sincronizando automaticamente o estado do cluster com a configuração armazenada no Git. Sua abordagem focada no CD - sem tentar resolver todo o fluxo de CI/CD - permite que equipes adotem entregas contínuas com maior maturidade, segurança e qualidade, tornando-se peça fundamental nas engenharias de software mais avançadas do mundo.

## 5.1 Criação do namespace do Argocd
Para instalar o ArgoCD como operador no Kubernetes, o primeiro passo é criar um namespace dedicado chamado argocd. Execute o comando abaixo para preparar o ambiente:
``` powershell
kubectl create namespace argocd
```
Este namespace isolará todos os recursos do ArgoCD, seguindo as melhores práticas de organização e segurança do cluster Kubernetes.

## 5.2 Instalando o argocd.
Agora vamos instalar o ArgoCD como um operador no Kubernetes:
``` powershell
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
Este comando baixa e aplica todos os manifestos necessários para ter o ArgoCD funcionando como controlador GitOps no seu cluster.

Vamos ver se os pods do ArgoCD foram criados com sucesso:

``` powershell
kubectl get pods -n argocd
```
Deve retornar:
``` powershell
NAME                                               READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                    1/1     Running   0          98s
argocd-applicationset-controller-79887fd6-d7zdj    1/1     Running   0          98s
argocd-dex-server-655b4448b5-kzhrv                 1/1     Running   0          98s
argocd-notifications-controller-77fd6f9885-hrtq8   1/1     Running   0          98s
argocd-redis-7fdcfb697b-df7mk                      1/1     Running   0          98s
argocd-repo-server-7d969c8c68-n95gv                1/1     Running   0          98s
argocd-server-58f6cdcccd-schqw                     1/1     Running   0          98s
```
Este output do ``` kubectl get pods ``` demonstra que a instalação do ArgoCD foi bem-sucedida, com todos os sete componentes principais em execução no cluster Kubernetes. Cada pod possui uma função específica: o ``` argocd-application-controller-0 ``` é o cérebro que sincroniza as aplicações entre o Git e o cluster; o ``` argocd-server ``` fornece a API e interface web; o ``` argocd-repo-server ``` gerencia os repositórios Git; enquanto componentes como ``` argocd-redis ``` e ``` argocd-dex-server ``` fornecem cache e autenticação.

## 5.3 Instalando Argocd CLI 
O ArgoCD CLI é uma ferramenta complementar que permite interagir com o ArgoCD diretamente pelo terminal, oferecendo controle total sobre aplicações, projetos e configurações através de comandos simples. Enquanto a interface web proporciona uma visão gráfica intuitiva, o CLI agiliza operações rotineiras, automações e integrações em pipelines CI/CD.

Para instalar a versão mais recente no Linux:
``` bash
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```
Para instalar no windows, vamos usar o Invoke-WebRequest no Power shell.

Invoke-WebRequest é um cmdlet nativo do PowerShell para fazer requisições HTTP/HTTPS, similar ao curl no Linux/Mac.

Primeiro vamos pegar a versão mais recente do argocd e armazenar na variável ```$version```.
```powershell
$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name
```
* ``` Invoke-RestMethod ```
   * Faz uma requisição à API do GitHub.

   * Consulta o endpoint de latest release do ArgoCD.

   * Retorna automaticamente como objeto (não como texto puro).

* ``` https://api.github.com/repos/argoproj/argo-cd/releases/latest ```
   * Endpoint oficial da API do GitHub.

   * Retorna informações da última versão estável do ArgoCD.

* ``` .tag_name ```
   * Acessa a propriedade que contém o número da versão. (Exemplo: "v2.8.4", "v2.9.0")

* ```$version =```
   * Armazena o resultado na variável $version para uso posterior
 
Substitua ``` $version ``` no comando abaixo pela versão do Argo CD que você deseja baixar:

```powershell
$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
$output = "argocd.exe"

Invoke-WebRequest -Uri $url -OutFile $outputkubectl 
```
Agora precisamos mover o arquivo para o seu PATH.

```powersheell
[Environment]::SetEnvironmentVariable("Path", "$env:Path;C:\Path\To\ArgoCD-CLI", "User")
```
Depois de concluir as instruções acima, você agora poderá executar ``` argocd ``` comandos, para fazer um teste rode o comando:

```powershell
argocd version
```
Vai retornar algo parecido com isso.

```powershell
argocd: v3.1.9+8665140
  BuildDate: 2025-10-17T22:07:41Z
  GitCommit: 8665140f96f6b238a20e578dba7f9aef91ddac51
  GitTreeState: clean
  GoVersion: go1.24.6
  Compiler: gc
  Platform: windows/amd64
```

## 5.4 Acessar o ArgoCD localmente
Para acessar o argocd precisamos fazer um port forwarding do Kubernetes que cria uma conexão segura e temporária entre uma máquina local e um recurso específico (normalmente um Pod ou Service) dentro de um cluster Kubernetes. Isso permite acesso local a aplicações ou serviços executando dentro do cluster como se estivessem rodando localmente, sem expô-los externamente. É utilizado principalmente para desenvolvimento, depuração e testes.

```powershell
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
* recomendo usar outro terminal para rodar esse comando, pois ele vai ficar rodando a aplicação.

Para acessar o argocd coloque o endereço ``` https://localhost:8080 ``` no seu navegador 

<img width="1790" height="636" alt="Captura de tela 2025-10-26 152553" src="https://github.com/user-attachments/assets/d969042f-08c6-487f-ad88-12f4993dc519" />

## 5.5 Fazendo login no argocd
Precisamos fazer login no argocd, o usuário padrão dele é ``` admin ``` e a senha temos que descobrir usando o comando:

**Para linux**
```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

**Como o windows não reconhece base64, temos que decodificar usando:**

```powershell
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | %{ [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }
```
* Decodificação do base64
  * % → Alias para ForEach-Object

  * [System.Convert]::FromBase64String($_) → Converte base64 para bytes

  * [System.Text.Encoding]::UTF8.GetString() → Converte bytes para texto legível
 
Descobrindo a senha podemos fazer o acesso no argocd com o comando:

```powershell
argocd login localhost:8080
```
Ele vai pedir o nome de usuário e depois a senha que descobrimos anteriormente, Depois ele estabelece a conexão.

``` powershell
'admin:login' logged in successfully
Context 'localhost:8080' updated
```
Podemos entrar pelo interface gráfica, colocando o nome de usuário e a senha.

## 5.6 Criando a Aplicação no ArgoCD.
Agora que o cluster Kubernetes está integrado ao ArgoCD, o próximo passo é criar a aplicação. Para isso, precisamos conectar um repositório Git contendo os manifestos Kubernetes da nossa aplicação. O ArgoCD monitorará continuamente este repositório e automaticamente implantará e sincronizará as alterações no cluster sempre que detectar atualizações no código-fonte.

Para criarmos a aplicação usamos o comando:

```powershell
argocd app create ola-app `
   --repo https://github.com/italolinux/ola-manifest.git `
   --path . `
   --dest-server https://kubernetes.default.svc `
   --dest-namespace default
```
**OBS:** Para o linux é o mesmo comando só trocar ``` ` ``` por ``` \ ```.

| Parâmetro          | Significado                                       |
| ------------------ | ------------------------------------------------- |
| `--repo`           | Link do repositório Git (com `.git`)              |
| `--path`           | Caminho dentro do repositório onde estão os YAMLs |
| `--dest-server`    | Cluster onde a app será implantada                |
| `--dest-namespace` | Namespace dentro do cluster                       |

Vamos verificar se a nossa aplicação foi criada.
```powershell
argocd app list
```
Retorna:

```powershell
NAME            CLUSTER                         NAMESPACE  PROJECT  STATUS     HEALTH   SYNCPOLICY  CONDITIONS  REPO                                            PATH  TARGET
argocd/ola-app  https://kubernetes.default.svc  default    default  OutOfSync  Healthy  Manual      <none>      https://github.com/italolinux/ola-manifest.git  .
```
Como podemos observar o status da nossa aplicação está ``` **OutOfSync** ``` e a health está ``` **Missing** ```, precisamos fazer o sync da aplicação com o argocd.

```powershell
argocd app sync ola-app
```
* O que acontece durante o ``` sync ```:
    
    *  Conecta ao repositório Git e busca os manifests mais recentes.

    * Compara o estado atual do cluster com o estado desejado no Git.

    * Aplica as diferenças no cluster Kubernetes.

    * Cria/atualiza todos os recursos definidos no deployment.yaml e service.yaml.

Vamos verificar se o pod da aplicação foi criado com o comando:

``` powershell
Kubectl get pods
```








