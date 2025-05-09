pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		DOCKER_HUB_REPO = 'chariis15/chariis-app'
		DOCKER_HUB_CREDENTIALS_ID = 'gitops-dockerhub'
	}
	stages {
		stage('Checkout Github'){
			steps {
				git branch: 'main', credentialsId: 'jenkins-access', url: 'https://github.com/Chariis/full-ci-cd.git'
				echo 'cloning github repo...'
			}
		}		
		stage('Install node dependencies'){
			steps {
				sh 'npm install'
				echo 'installing node dependencies...'
			}
		}
		stage('Build Docker Image'){
			steps {
				script {
					dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
					echo 'building docker image...'
				}
			}
		}
		stage('Trivy Scan'){
			steps {
				echo 'scanning docker image with trivy...'
				sh 'trivy --severity HIGH,CRITICAL --skip-update --no-progress image --format table -o trivy-scan-report.txt ${DOCKER_HUB_REPO}:latest'
			}
		}
		stage('Push Image to DockerHub'){
			steps {
				script {
					echo 'pushing docker image to DockerHub...'
					docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}") {dockerImage.push('latest')
					}
				}
			}
		}
		stage('Install Kubectl & ArgoCD CLI'){
			steps {
				echo 'installing kubectl & argocd cli...'
			}
		}
		stage('Apply Kubernetes Manifests & Sync App with ArgoCD'){
			steps {
				echo 'applying kubernetes manifests...'
			}		
		}
	}

	post {
		success {
			echo 'Build & Deploy completed succesfully!'
		}
		failure {
			echo 'Build & Deploy failed. Check logs.'
		}
	}
}
