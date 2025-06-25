pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nikhil09871/mern-chat-app"
        K8S_DEPLOYMENT = "mern-chat-deployment"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', credentialsId: 'github-pat', url: 'https://github.com/nikhil09871/CHATAPPLICATION.git'
            }
        }
        
	stage('Install Node.js') {
    	     steps {
                 sh '''
                 curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                 sudo apt install -y nodejs
        	'''
 	   }
}
	
        stage('Install Dependencies') {
            steps {
                sh 'cd fullstack-chat-app-master/backend && npm install'
                sh 'cd fullstack-chat-app-master/frontend && npm install'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE-backend ./backend'
                sh 'docker build -t $DOCKER_IMAGE-frontend ./frontend'
            }
        }

        stage('Push Docker Images') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh 'docker push $DOCKER_IMAGE-backend'
                    sh 'docker push $DOCKER_IMAGE-frontend'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}

