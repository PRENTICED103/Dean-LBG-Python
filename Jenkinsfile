pipeline {

    agent any

    stages {

         stage('Init') {

            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.214.0.2 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                docker rmi prentice103/python-api || echo "image does not exist"
                                '''
             }
             }

        stage('Build') {

            steps {
                sh '''
                docker build -t prenticed103/python-api -t prenticed103/python-api:v${BUILD_NUMBER} .
                               '''
            }

        }

  stage('Push') {

            steps {
                sh '''
                docker push prenticed103/python-api
                docker push prenticed103/python-api:v${BUILD_NUMBER}
                           
                '''
            }
  }
        stage('Deploy') {

            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.214.0.2 << EOF
                docker run -d -p 80:8080 --name flask-app prenticed103/python-api
                '''
            }

        }
stage('Clean Up') {

            steps {
                sh '''
                docker system prune -f
                '''  
                            }
     

    }
  }
    }
