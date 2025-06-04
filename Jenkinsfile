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
        timestamps()
    }

    stages {
        stage('🔍 Debug Info') {
            steps {
                echo "Build número: ${env.BUILD_ID}"
                echo "Workspace: ${env.WORKSPACE}"
                echo "Branch: ${env.BRANCH_NAME ?: 'não definido'}"
                sh 'docker --version || echo "Docker não encontrado"'
                sh 'kubectl version --client || echo "kubectl não encontrado"'
            }
        }

        stage('📦 Build Docker Image') {
            steps {
                script {
                    echo "Iniciando build da imagem Docker: ${DOCKER_IMAGE}:${IMAGE_TAG}"
                    dockerImage = docker.build("${DOCKER_IMAGE}:${IMAGE_TAG}", "-f ./src/Dockerfile ./src")
                    echo "Imagem Docker criada: ${dockerImage.imageName()}"
                }
            }
        }

        stage('🚀 Push Docker Image') {
            steps {
                script {
                    echo "Autenticando no Docker Hub e fazendo push da imagem..."
                    docker.withRegistry(REGISTRY_URL, REGISTRY_CREDENTIALS) {
                        dockerImage.push('latest')
                        dockerImage.push("${IMAGE_TAG}")
                    }
                    echo "Push concluído."
                }
            }
        }

        stage('☸️ Deploy no Kubernetes') {
            steps {
                script {
                    echo "Iniciando deploy no Kubernetes com tag: ${IMAGE_TAG}"
                    withKubeConfig([credentialsId: KUBECONFIG_CREDENTIALS]) {
                        sh 'cp ./k8s/deployment.yaml ./k8s/deployment.yaml.bkp'
                        sh "sed -i 's/{{tag}}/${IMAGE_TAG}/g' ./k8s/deployment.yaml"
                        sh 'kubectl apply -f ./k8s/deployment.yaml'
                        sh 'mv ./k8s/deployment.yaml.bkp ./k8s/deployment.yaml'
                    }
                    echo "Deploy aplicado com sucesso."
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executado com sucesso!"
        }
        failure {
            echo "❌ Pipeline falhou! Verifique o console para mais detalhes."
        }
        always {
            cleanWs()
        }
    }
}
