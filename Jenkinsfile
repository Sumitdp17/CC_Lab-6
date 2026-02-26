pipeline {
    agent any

    stages {

        stage('Prepare Network') {
            steps {
                sh '''
                docker rm -f backend1 backend2 nginx-lb 2>/dev/null || true
                docker network rm labnet 2>/dev/null || true
                docker network create labnet
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app 2>/dev/null || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Run Backends') {
            steps {
                sh '''
                docker run -d --network labnet --name backend1 backend-app
                docker run -d --network labnet --name backend2 backend-app
                sleep 5
                '''
            }
        }

        stage('Run NGINX') {
            steps {
                sh '''
                docker run -d -p 80:80 --network labnet --name nginx-lb nginx
                sleep 3
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}
