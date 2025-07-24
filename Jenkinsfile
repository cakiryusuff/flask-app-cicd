pipeline {
  agent any
  stages {
	stage('checkout Github'){
		steps{
			echo 'checking out code from github...'
			checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-token', url: 'https://github.com/cakiryusuff/flask-app-cicd.git']])
		}
}

	stage('build docker image'){
		steps{
			echo 'Building Docker Image...'		
		}
	}
	stage('push image to dockerhub'){
		steps{
			echo 'pushing docker image to dockerhub'
		}
	}
}
}
