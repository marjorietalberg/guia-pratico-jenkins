pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "marjorietalberg/guia-jenkins"
        IMAGE_TAG = "${env.BUILD_ID}"
        REGISTRY_URL = 'https://registry.hub.docker.com'
        REGISTRY_CREDENTIALS = 'dockerhub'
        KUBECONFIG_CREDENTIALS = 'kubeconfig'
    }

    options {
        timestamps() // timestamps nos logs
    }

    stages {
        stage('📦 Build Docker Image') {
            steps {
                wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']) {
                    script {
                        echo "\u001B[34m🔨 Iniciando build da imagem Docker: ${DOCKER_IMAGE}:${IMAGE_TAG}\u001B[0m"
                        dockerImage = docker.build("${DOCKER_IMAGE}:${IMAGE_TAG}", "-f ./src/Dockerfile ./src")
                    }
                }
            }
        }

        stage('🚀 Push Docker Image') {
            steps {
                wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']) {
                    script {
                        echo "\u001B[35m🚀 Fazendo push da imagem para o Docker Hub...\u001B[0m"
                        docker.withRegistry("${REGISTRY_URL}", "${REGISTRY_CREDENTIALS}") {
                            dockerImage.push('latest')
                            dockerImage.push("${IMAGE_TAG}")
                        }
                    }
                }
            }
        }

        stage('☸️ Deploy no Kubernetes') {
            steps {
                wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']) {
                    script {
                        echo "\u001B[36m☸️ Fazendo deploy no cluster Kubernetes com a imagem ${IMAGE_TAG}\u001B[0m"
                        withKubeConfig([credentialsId: "${KUBECONFIG_CREDENTIALS}"]) {
                            // Backup do YAML
                            sh 'cp ./k8s/deployment.yaml ./k8s/deployment.yaml.bkp'
                            // Atualiza tag no YAML
                            sh "sed -i 's/{{tag}}/${IMAGE_TAG}/g' ./k8s/deployment.yaml"
                            // Aplica no cluster
                            sh 'kubectl apply -f ./k8s/deployment.yaml'
                            // Restaura arquivo original
                            sh 'mv ./k8s/deployment.yaml.bkp ./k8s/deployment.yaml'
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "\u001B[32m✅ Pipeline executado com sucesso! Deploy concluído.\u001B[0m"
        }
        failure {
            echo "\u001B[31m❌ Pipeline falhou! Verifique os erros nos logs.\u001B[0m"
        }
        always {
            cleanWs()
        }
    }
}
