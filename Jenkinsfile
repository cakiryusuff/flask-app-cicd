pipeline {
  agent any
  environment {
	DOCKER_HUB_REPO = 'cakiryusuff1/gitops-jenkinsargocd-project'
	DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-token'
  }
  stages {
	stage('checkout Github'){
		steps{
			echo 'checking out code from github....'
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
	stage("Install Kubectl & ArgoCD CLI") {
		steps{
			sh '''
				echo 'installing Kubectl & ArgoCD cli...'
				curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
				chmod +x kubectl
				mv kubectl /usr/local/bin/kubectl
				curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
				chmod +x /usr/local/bin/argocd
			'''
		}
	}
	stage('apply kubernetes & sync app with ArgoCD'){
		steps{
			script{
				kubeconfig(credentialsId: 'kubeconfig', serverUrl: 'https://192.168.49.2:8443') {
					sh '''
					argocd login 34.140.241.165:32603 --username admin --password $(kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d) --insecure
					argocd app sync gitopsapp
					'''
					}
				}
			}
		}
	}
}
