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

* Repository name*: Colocar o nome do repositório no meu caso **ola-app**
* Description: Colocar uma pequena descrição sobre o projeto.
* Choose visibility*: Opção para escolher a visibilidade do repositório deixaremos **Public**
* Add README : **on**
* Add .gitignore: **No .gitignore**
* Add license - **No license**
* Para finalizar clique em **Create Repository**
