pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
        DOCKER_IMAGE = "shyam2210/nodeimage7:${env.BUILD_NUMBER}"
    }
	
	stages {
		stage('Checkout Github'){
			steps {
				git branch: 'main', credentialsId: 'jen-doc-git', url: 'https://github.com/Shyam-Patoliya/NodeApp.git'
			}
		}		
		stage('Install node dependencies'){
			steps {
				sh 'npm install'
			}
		}
		stage('Test Code'){
			steps {
				sh 'npm test'
			}
		}
		stage('Build and Push Docker Image') {
            steps {
                script {
                    // Log in to Docker Hub using the credentials we set up in Jenkins
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        // Build the image using the DOCKER_IMAGE variable
                        def customImage = docker.build("${DOCKER_IMAGE}", "--pull .")
                        // Push the image to the registry
                        customImage.push()
                    }
                }
            }
        }
		stage('Deploy to Kubernetes') {
		    steps {
		        withKubeConfig([credentialsId: 'kubernetes-token-secret']) {
		            sh 'kubectl set image deployment/nodeimage7-deployment nodeimage7-container=shyam2210/nodeimage7:29'
		        }
		    }
		}

	}
		
	post {
		success {
			echo 'Build&Deploy completed succesfully!'
		}
		failure {
			echo 'Build&Deploy failed. Check logs.'
		}
	}
}
