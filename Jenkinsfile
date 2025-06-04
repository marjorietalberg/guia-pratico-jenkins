pipeline {
    agent any

    stages {
        stage('Build a Docker Image') {
            steps {
                echo "Iniciando build da imagem Docker..."
                script {
                    dockerapp = docker.build("andrrade/guia-jenkins:${env.BUILD_ID}",'-f ./src/Dockerfile ./src')
                }
            }
        }

        stage('Push a Docker Image') {
            steps {
                echo "Fazendo push da imagem Docker..."
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                echo "Iniciando deploy no Kubernetes com tag ${tag_version}..."
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }
    }
}
