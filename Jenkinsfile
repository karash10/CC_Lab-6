pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                echo "Cloning repository..."
            }
        }

        stage('Build Backend Image') {
            steps {
                echo "Building backend Docker image..."
                sh '''
                cd CC_LAB-6
                docker build -t backend-app backend
                '''
            }
        }

        stage('Remove Old Containers') {
            steps {
                echo "Removing old backend and nginx containers if they exist..."
                sh '''
                docker rm -f backend1 backend2 nginx-lb || true
                '''
            }
        }

        stage('Deploy Backends') {
            steps {
                echo "Starting backend containers..."
                sh '''
                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app
                '''
                sh 'sleep 3'
            }
        }

        stage('Start NGINX Load Balancer') {
            steps {
                echo "Starting nginx load balancer..."
                sh '''
                docker run -d --name nginx-lb -p 80:80 nginx
                '''
                sh 'sleep 2'
            }
        }

        stage('Configure NGINX') {
            steps {
                echo "Copying nginx configuration..."
                sh '''
                docker cp CC_LAB-6/nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed. Check console logs."
        }
    }
}
