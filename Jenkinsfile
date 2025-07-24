pipeline {
  agent any
  environment {
	DOCKER_HUB_REPO = 'cakiryusuff1/gitops-jenkinsargocd-project'
	DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-token'
  }
  stages {
	stage('checkout Github'){
		steps{
			echo 'checking out code from github...'
			checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-token', url: 'https://github.com/cakiryusuff/flask-app-cicd.git']])
		}
		}

	stage('build docker image'){
		steps{
			script {
				echo 'building docker image...'
				dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
			}	
		}
	}
	stage('push image to dockerhub'){
		steps{
			script {
				echo 'pushing docker image to dockerhub...'
				docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}") {
					dockerImage.push('latest')
				}
			}
		}
	}
}
}
