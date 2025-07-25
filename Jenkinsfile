pipeline {
  agent any
  stages {
	stage('checkout Github'){
		steps{
			echo 'checking out code from github....'
			checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-token', url: 'https://github.com/cakiryusuff/flask-app-cicd.git']])
		}
	}
    stage('Build Image') {
      steps {
        script {
          docker.build("flask-app:latest")
        }
      }
    }
    stage('Load to Minikube') {
      steps {
        sh 'minikube image load flask-app:latest'
      }
    }
  }
}
