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
					argocd login 35.189.198.231:32017 --username admin --password $(kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d) --insecure
					argocd app sync gitopsapp
					'''
					}
				}
			}
	}
  }
}
