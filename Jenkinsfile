pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "marjorietalberg/guia-jenkins"
    }

    stages {
        stage('Build a Docker Image') {
            steps {
                script {
                    dockerApp = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage('Push a Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        dockerApp.push('latest')
                        dockerApp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubeconfig']) {
                        sh "sed -i 's/{{tag}}/${tag_version}/g' ./k8s/deployment.yaml"
                        sh 'kubectl apply -f ./k8s/deployment.yaml'
                    }
                }
            }
        }
    }
}
