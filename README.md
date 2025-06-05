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
    <td width="40%">
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
    <td width="40%">
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


