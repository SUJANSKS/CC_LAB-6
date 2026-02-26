pipeline {
    agent any

    stages {

        stage('Clean Old Containers & Network') {
            steps {
                sh '''
                docker rm -f backend1 backend2 backend-1 backend-2 nginx || true
                docker network rm app-network || true
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Create Network') {
            steps {
                sh '''
                docker network create app-network
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker run -d --name backend1 --network app-network backend-app
                docker run -d --name backend2 --network app-network backend-app
                '''
            }
        }

        stage('Build NGINX Image') {
            steps {
                sh '''
                docker rmi -f nginx-lb || true
                docker build -t nginx-lb nginx
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker run -d \
                  --name nginx \
                  --network app-network \
                  -p 8082:80 \
                  nginx-lb
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully. Access app at http://localhost:8082'
        }
        failure {
            echo 'Pipeline failed. Check console output.'
        }
    }
}
