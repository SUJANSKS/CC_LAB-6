pipeline {
    agent any

    stages {

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
                docker network create app-network || true
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
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

        stage('Deploy NGINX') {
            steps {
                sh '''
                docker rm -f nginx || true
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
            echo 'Pipeline executed successfully. Load balancer running on port 8082.'
        }
        failure {
            echo 'Pipeline failed. Check console logs.'
        }
    }
}
