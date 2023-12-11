pipeline {

    agent any

    stages {

         stage('Init') {

            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.214.0.2 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                docker stop nginx || echo "nginx not running"
                docker rm nginx || echo "nginx not running"
                docker rmi prenticed103/python-api || echo "image does not exist"
                docker rmi prenticed103/flask-nginx || echo "image does not exist"
                docker network create stretch_project1 || echo "network alreday exists"
                                '''
             }
             }

        stage('Build') {

            steps {
                sh '''
                docker build -t prenticed103/python-api -t prenticed103/python-api:v${BUILD_NUMBER} .
                docker build -t prenticed103/flask-nginx -t prenticed103/flask-nginx:v${BUILD_NUMBER} ./nginx
                               '''
            }

        }

  stage('Push') {

            steps {
                sh '''
                docker push prenticed103/python-api
                docker push prenticed103/python-api:v${BUILD_NUMBER}
                docker push prenticed103/flask-nginx
                docker push prenticed103/flask-nginx:v${BUILD_NUMBER}
                        
                '''
            }
  }
        stage('Deploy') {

            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.214.0.2 << EOF
                docker run -d --name flask-app --network stretch_project1 prenticed103/python-api 
                docker run -d -p 80:80 nginx --network stretch_project1 prenticed103/flask-nginx
                '''
            }

        }
stage('Clean Up') {

            steps {
                sh '''
                docker system prune -f
                docker rmi python-api:v${BUILD_NUMBER}
                docker rmi prenticed103/flask-nginx:v${BUILD_NUMBER}
                '''  
                            }
     

    }
  }
    }
