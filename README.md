# Implementação de Pipeline CI/CD Local com Jenkins + Kubernetes 
<div align="center">
  <a href="https://hub.docker.com/_/jenkins" target="_blank">
    <img src="https://skillicons.dev/icons?i=docker" alt="Docker - Jenkins"/>
  </a>
  <a href="https://www.jenkins.io/doc/" target="_blank">
    <img src="https://skillicons.dev/icons?i=jenkins" alt="Jenkins"/>
  </a>
  <a href="https://kubernetes.io/docs/" target="_blank">
    <img src="https://skillicons.dev/icons?i=kubernetes" alt="Kubernetes"/>
  </a>
</div>


 <table>
  <tr>
    <td width="900px">
      <h2>🎯 Objetivo</h2>
      <p>
        O objetivo deste projeto foi realizar a configuração e execução de um pipeline de <b>CI/CD</b> totalmente em ambiente <b>local</b>, utilizando o sistema operacional <b>Ubuntu</b>.<br><br>
        Foram realizadas as seguintes etapas:<br>
        - 🐳 <b>Instalação do Docker</b> no ambiente local para gerenciamento de containers.<br>
        - ⚙️ <b>Implantação do Jenkins</b>, utilizando Docker, para orquestrar e automatizar processos de integração e entrega contínua (<b>CI/CD</b>).<br>
        - 🔧 Configuração do <b>pipeline CI/CD no Jenkins</b>, que executa as etapas de build, teste e deploy automatizado.<br>
        - ☸️ Integração com o <b>Kubernetes local</b>, para realizar o deploy das aplicações de forma automatizada e escalável dentro dos clusters.<br><br>
      </p>
    </td>
    <td width="900px">
      <img src="https://github.com/user-attachments/assets/1615189d-6447-46b5-9414-bbd96af02ec4" alt="Projeto CI/CD" height="350px" />
    </td>
  </tr>
</table>

####   Todo o processo foi realizado localmente, sem uso de serviços em nuvem, permitindo total controle do ambiente e proporcionando aprendizado prático sobre automação, integração e deploy de aplicações com <b>Jenkins</b>, <b>Docker</b> e <b>Kubernetes</b>.
### Instalação do Ambiente

#### Java (JDK 17)

Para instalar o Java JDK 17, execute os seguintes comandos:

```bash
sudo apt update && sudo apt install openjdk-17-jdk -y
```

#### Jenkins

Para instalar o Jenkins, siga os passos abaixo:

1. Adicione a chave do Jenkins ao seu sistema:

    ```bash
    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
      https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    ```

2. Adicione o repositório do Jenkins à lista de fontes do apt:

    ```bash
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    ```

3. Atualize a lista de pacotes e instale o Jenkins:

    ```bash
    sudo apt-get update && sudo apt-get install -y jenkins
    ```

#### Chave de Segurança

Para obter a senha inicial de administrador do Jenkins, execute o comando:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

#### Docker

Para instalar o Docker, siga os passos abaixo:

1. Adicione a chave GPG oficial do Docker:

    ```bash
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    ```

2. Adicione o repositório Docker às fontes do apt:

    ```bash
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```

3. Instale o Docker e seus componentes:

    ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
    ```

4. Adicione o usuário atual e o usuário Jenkins ao grupo docker:

    ```bash
    sudo usermod -aG docker $USER
    sudo usermod -aG docker jenkins
    ```

5. Reinicie o Jenkins para aplicar as permissões no Docker:

    ```bash
    sudo systemctl restart jenkins
    ```

#### Kubectl

Para instalar o kubectl, siga os passos abaixo:

1. Adicione as chaves e repositórios necessários:

    ```bash
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    ```

2. Adicione o repositório do Kubernetes:

    ```bash
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
    ```

3. Atualize a lista de pacotes e instale o kubectl:

    ```bash
    sudo apt-get update
    sudo apt-get install -y kubectl
    ```


### Automatizando Deploy com Jenkins: Resultado Final + Passo a Passo com Imagens

### 1º Passo – Acessar o Jenkins no Navegador
  >  Após subir o container do Jenkins, acesse o Jenkins através do seu navegador utilizando seu endereço
  >  O Jenkins solicita uma senha inicial para desbloqueio. Essa senha é gerada automaticamente na criação do container.
  >  ➡️ Para obter a senha, execute no terminal:
```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
<img src="https://github.com/user-attachments/assets/479ef5e8-bb1d-4584-98dc-877eb8dc2261" alt="Image">

### 2º Passo – Criar sua Conta no Jenkins (Usuário Admin)
  > Após concluir a instalação dos plugins, o Jenkins irá direcionar você para a tela de criação do primeiro usuário administrador.

  >  ➡️ Preencha o formulário com seus dados:
  >  Username: → Seu nome de usuário (será usado para login no Jenkins) 

 > Password: → Sua senha (crie uma senha forte e segura)

 > Confirm password: → Confirme sua senha

 > Full name: → Seu nome completo

 > Email address: → Seu e-mail (importante para notificações do Jenkins)

<img src="https://github.com/user-attachments/assets/eed9e951-292c-48bd-851c-0b88ae302cf9" alt="Image">

### 3º Passo – Customizar o Jenkins (Instalação dos Plugins)
  >  Após inserir a senha inicial, o Jenkins oferece a opção de personalizar a instalação com plugins.
> 
  >  ✔️ Escolha a Opção: Install Suggested Plugins
   >  Essa opção é a mais recomendada para quem está começando, pois instala os plugins essenciais para rodar a maioria dos pipelines.

  > O Jenkins começará automaticamente a instalação dos plugins padrão, como:
  > Git ,Pipeline, SSH Agent, Docker Pipeline, Blue Ocean
  > E outros essenciais para integração contínua e deploy.

<img src="https://github.com/user-attachments/assets/f276f09c-e8b3-4ed4-a3cb-b926719a8810" alt="Image">

### 4º Passo – Acessar o Painel Principal e Ir em “Gerenciar Jenkins”
  >  Após criar sua conta e finalizar a configuração inicial, você será redirecionado para o Painel Principal do Jenkins. 
 > Essa seção é o painel central de administração do Jenkins, onde você pode gerenciar configurações, instalar e atualizar plugins, controlar a segurança, configurar ferramentas e realizar outras tarefas administrativas essenciais.

<img src="https://github.com/user-attachments/assets/b68d27f2-5d2a-4f3a-ba7e-76639d203cbe" alt="Image">

###  5º Passo – Acessando os Plugins no Jenkins
 >  Após acessar “Gerenciar Jenkins”, você poderá visualizar várias opções de configuração.


<img src="https://github.com/user-attachments/assets/977f1d57-0e8e-47c0-a8b0-2c08d8af4673" alt="Image">

### 6º Passo – Instalar os Plugins Necessários (Docker, Docker Pipeline, Kubernetes CLI)
 >  Agora dentro da seção “Gerenciar Plugins”, siga os passos abaixo para instalar os plugins essenciais que permitirão criar pipelines modernos e integrar seu Jenkins com containers e clusters Kubernetes.
Passo a passo para instalar os plugins:
> 
 >  1️⃣ Clique na aba “Disponíveis”
 >  ➡️ Aqui estão todos os plugins que podem ser instalados no Jenkins.

 >  2️⃣ Na barra de busca, pesquise e selecione os seguintes plugins:

 > 🔹 Docker → Permite que o Jenkins interaja com o Docker Host.

 >  🔹 Docker Pipeline → Adiciona suporte para executar etapas de pipeline diretamente em containers Docker.

 > 🔹 Kubernetes CLI Plugin → Permite que pipelines do Jenkins executem comandos no Kubernetes via kubectl diretamente do Jenkins.

 >  3️⃣ Marque a caixinha de cada plugin encontrado.

<img src="https://github.com/user-attachments/assets/f049add8-3888-4ba5-8717-52913ca0036d" alt="Image">

### 7º Passo – Criar um Novo Pipeline no Jenkins
 >  Com os plugins já instalados, agora é hora de criar o seu primeiro pipeline no Jenkins. Essa etapa é essencial para automatizar o processo de build, teste  > e deploy da sua aplicação.

 >  1️⃣ No Painel Principal do Jenkins, clique em:
 Novo Item

 > 2️⃣ Na tela que abrir:
 > 📝 Dê um nome ao seu projeto (Ex: pipeline-docker-k8s)

 >  ✅ Selecione a opção "Pipeline"

Clique em OK

<img src="https://github.com/user-attachments/assets/e9fa7ce8-e838-4540-a594-2f8a83cfe16d" alt="Image">

##  8º Passo  – Configurar o Pipeline com Script do Git
 >  Agora que o pipeline foi criado, é hora de conectá-lo ao seu repositório Git, onde está o Jenkinsfile com as etapas da automação (build, test, deploy etc.).
Acesse as configurações do pipeline criado.

 > Vá até a seção Pipeline.

 > Altere o campo Definição para "Pipeline script from SCM".

 > Escolha Git como sistema de controle de versão.

 > Insira a URL do seu repositório Git onde está o Jenkinsfile.

 > Se necessário, adicione suas credenciais (caso o repositório seja privado).

 > No campo Branches to build, substitua */master por */main.

 > Clique em Salvar para aplicar as configurações.

#### ✅ Objetivo:
Conectar o Jenkins ao seu repositório Git para que ele busque e execute o Jenkinsfile diretamente do branch main.

<img src="https://github.com/user-attachments/assets/cb380e30-3c1f-474d-8e1a-e8f4dcc67bde" alt="Image">

### 9º Passo – Pipeline Funcionando com Sucesso
 >  📌 O que aconteceu:
 >  Após configurar corretamente o pipeline com o repositório Git, o Jenkins buscou o Jenkinsfile no branch main.

 >  O pipeline foi executado automaticamente com base nas etapas definidas no Jenkinsfile.

 >  O console do Jenkins exibiu o processo de build, testes e deploy conforme configurado.

 >  O status da execução foi indicado como "Sucesso" com um ícone verde na interface.

<img src="https://github.com/user-attachments/assets/725212d0-7b64-489b-9550-0ad5a91be899" alt="Image">

 ### Execução do Pipeline – Jenkins
 >  Após o pipeline ter sido executado com sucesso, essas foram as etapas (stages) processadas:

 > 🗂️ Stage: Checkout SCM
Tempo: 0,65s

Descrição: Nesta etapa, o Jenkins realizou o checkout do repositório Git, baixando os arquivos necessários para a execução do pipeline.

 > 🔄 Stage: Checkout SCM (repetição)
Tempo: 0,5s

Descrição: Uma segunda verificação ou pull do repositório foi feita, comum em pipelines mais complexos ou que usam múltiplos workspaces.

 > 🛠️ Stage: Build Docker Image
Tempo: 1,9s

Descrição: O Jenkins construiu a imagem Docker definida no Dockerfile, empacotando a aplicação com suas dependências.

 > 📤 Stage: Push Docker Image
Tempo: 11s

 > Descrição: A imagem Docker foi enviada (push) para o repositório de imagens (Docker Hub ou outro registry), tornando-a disponível para deploy.

<img src="https://github.com/user-attachments/assets/c167f510-5b67-437f-a4b8-80f2d5c0a9a0" alt="Image">

### 📌 Etapa Final – Aplicação Executando no Docker

 > O Jenkins construiu e enviou a imagem Docker com sucesso para o repositório.

 > Em seguida, a imagem foi utilizada para subir um container Docker.

 > Esse container está executando a aplicação web de conversão de temperatura, com base no arquivo index.ejs.

<img src="https://github.com/user-attachments/assets/38047f04-97ab-46c5-a387-11c46a16b67a" alt="Image">

### Frontend Estilizado da Aplicação de Conversão de Temperatura Rodando no Docker
<img src="https://github.com/user-attachments/assets/0b175813-3d4f-49f2-b6d8-5b59cbd120a6" alt="Image">

---
### 🔗 Observações – Como Configurar o Webhook no GitHub para Jenkins
 > Para que seu Jenkins dispare automaticamente a pipeline sempre que houver um push no repositório GitHub, você precisa configurar um webhook. Aqui estão as instruções passo a passo:

 > 🧩 Etapas no Jenkins:
 > Acesse o painel principal do Jenkins.

> Vá em Gerenciar Jenkins > Gerenciar Plugins e verifique se o plugin "GitHub Integration Plugin" está instalado.

> Depois, vá em Gerenciar Jenkins > Configurar o Sistema.

> Na seção GitHub, adicione as credenciais do GitHub (token pessoal).

 > Em seguida, no seu job/pipeline, vá em Configurar.

Marque a opção:
 > ✅ "GitHub hook trigger for GITScm polling"

 > 🌐 Etapas no GitHub:
Acesse o repositório onde está seu código.

> Vá até Settings > Webhooks.

> Clique em “Add webhook”.

> Preencha os campos assim:

> Payload URL:
> ( IP público do seu servidor Jenkins)

> Content type:
> application/json

 > Which events would you like to trigger this webhook?
> ✅ Just the push event

> Marque a opção Active.

> Clique em “Add webhook”.

 > ✅ Resultado esperado:
> A cada push no GitHub, o webhook envia um evento para o Jenkins.

> O Jenkins, por sua vez, executa automaticamente o pipeline, buscando as atualizações no seu repositório.

<img src="https://github.com/user-attachments/assets/7d639192-623d-4e92-aa5b-d9be496804af" alt="Image 1">

<img src="https://github.com/user-attachments/assets/123d09f4-7b5b-46e7-b05e-03dfd977bef0" alt="Image 2">

