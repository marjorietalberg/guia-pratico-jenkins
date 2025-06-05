pipeline {
  agent any

  stages {
    stage('Build Docker Image') {
      steps {
        script {
          docker.build("marjorie02/guia-jenkins:2", "./src")
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        withDockerRegistry(credentialsId: 'dockerhub') {
          script {
            docker.image("marjorie02/guia-jenkins:2").push()
          }
        }
      }
    }
  }
}
