pipeline {
    agent any

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                    docker network create lab6-net || true
                    docker rm -f backend1 backend2 nginx-lb || true
                    docker rmi -f backend-app || true
                    docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                    docker run -d --name backend1 --network lab6-net backend-app
                    docker run -d --name backend2 --network lab6-net backend-app
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                    docker run -d --name nginx-lb \
                      --network lab6-net \
                      -p 80:80 nginx
                    sleep 3
                    docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                    docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}
