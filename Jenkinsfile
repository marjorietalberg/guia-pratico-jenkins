pipeline {
    agent any

    environment {
        IMAGE_NAME = "marjorie02/guia-jenkins"
        IMAGE_TAG = "2"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git 'https://github.com/marjorietalberg/guia-pratico-jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}", "-f ./src/Dockerfile ./src")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(
                        url: 'https://index.docker.io/v1/',  // Docker Hub
                        credentialsId: 'dockerhub'
                    ) {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                    }
                }
            }
        }
    }
}
