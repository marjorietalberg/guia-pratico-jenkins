pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                echo '🔗 Clonando o repositório...'
                git branch: 'main', url: 'https://github.com/marjorietalberg/guia-pratico-jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo '🔨 Construindo imagem Docker...'
                    dockerImage = docker.build('guia-jenkins:latest', './src')
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    echo '🚀 Subindo container local...'
                    dockerImage.run('-d -p 8080:3000')
                }
            }
        }
    }

    post {
        always {
            echo '✅ Pipeline finalizado.'
        }
        failure {
            echo '❌ Pipeline falhou.'
        }
    }
}
