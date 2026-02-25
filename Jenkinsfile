pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app ./backend'
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

        stage('Deploy Nginx') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d \
                  --name nginx-lb \
                  -p 8081:80 \
                  --link backend1 \
                  --link backend2 \
                  -v $PWD/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                  nginx
                '''
            }
        }
    }
}
