pipeline {
    agent any

    environment {
        IMAGE_NAME = "marjorietalberg/guia-jenkins"
        TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Clonando o repositório...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo '🔨 Construindo imagem Docker...'
                    dockerImage = docker.build("${IMAGE_NAME}:${TAG}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    echo '🚀 Enviando imagem para o DockerHub...'
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerImage.push('latest')
                        dockerImage.push("${TAG}")
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo '📦 Fazendo deploy no Kubernetes...'
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://192.168.1.81:6443']) {
                    sh """
                    sed -i 's/{{tag}}/${TAG}/g' ./k8s/deployment.yaml
                    kubectl apply -f ./k8s/deployment.yaml
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finalizado com sucesso!'
        }
        failure {
            echo '❌ Pipeline falhou.'
        }
    }
}
