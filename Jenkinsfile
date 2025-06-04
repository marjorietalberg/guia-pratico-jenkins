pipeline {
    agent any

    stages {
        stage('Build a Docker Image') {
            steps {
                script {
                    // Construindo a imagem com tag usando o BUILD_ID do Jenkins
                    dockerapp = docker.build("marjorietalberg/guia-jenkins:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage('Push a Docker Image') {
            steps {
                script {
                    // Fazendo push para o Docker Hub com as credenciais 'dockerhub'
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')                  // Push da tag latest
                        dockerapp.push("${env.BUILD_ID}")         // Push da tag do build atual
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }
    }
}
