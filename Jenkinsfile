pipeline {
  agent any
  environment {
    DOCKER_HUB_REPO = 'cakiryusuff1/gitops-jenkinsargocd-project'
    DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-token'
  }
  stages {
    stage('Checkout from GitHub') {
      steps {
        echo 'Checking out code from GitHub...'
        git(
          url: 'https://github.com/cakiryusuff/flask-app-cicd.git',
          branch: 'main',
          credentialsId: 'github-token'
        )
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          echo 'Building Docker image...'
          dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
        }
      }
    }
    stage('Push Image to Docker Hub') {
      steps {
        script {
          echo 'Pushing Docker image to Docker Hub...'
          docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}") {
            dockerImage.push('latest')
          }
        }
      }
    }
    stage('Load Image to Minikube') {
      steps {
        script {
          echo 'Loading Docker image to Minikube...'
          // Minikube docker ortamına erişimin varsa:
          sh "minikube image load ${DOCKER_HUB_REPO}:latest"
          // Eğer Jenkins container içinden çalışıyorsan minikube docker socket mount lazım.
        }
      }
    }
  }
}
