pipeline {
    agent any

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d \
                  --name nginx-lb \
                  -p 8081:80 \
                  -v $WORKSPACE/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                  --link backend1 \
                  --link backend2 \
                  nginx:latest
                '''
            }
        }

    }

    post {
        success {
            echo 'Pipeline executed successfully. NGINX load balancer is running.'
        }
    }
}
