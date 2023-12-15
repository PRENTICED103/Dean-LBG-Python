pipeline {
    agent any
    stages {
      stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main")  {
                        sh '''
                        kubectl create namespace prod || echo "Namespace prod already exists"
                        '''
                     } else if (env.GIT_BRANCH == "origin/dev")  {
                        sh '''
                        kubectl create namespace dev || echo "Namespace dev alreday exists"
                        '''
                        } else {
                        sh '''
                         echo "Branch not recognised"
                         '''
                    }
            }
                }
      }
        stage('Build') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main")  {
                        sh '''
                        docker build -t prenticed103/flask-jenk:latest -t prenticed103/flask-jenk:prod-v${BUILD_NUMBER} .
                        '''
                     } else if (env.GIT_BRANCH == "origin/dev")  {
                        sh '''
                        docker build -t prenticed103/flask-jenk:latest -t prenticed103/flask-jenk:dev-v${BUILD_NUMBER} .
                        '''
                     } else {
                        sh '''
                         echo "Branch not recognised"
                         '''
                    }
            }
                }
                        }

        stage('Push') {
            steps {
                script {
                if (env.GIT_BRANCH == "origin/main")  {
                sh '''
                docker push prenticed103/flask-jenk:latest
                docker push prenticed103/flask-jenk:prod-v${BUILD_NUMBER}
                  '''
                } else if (env.GIT_BRANCH == "origin/dev")  {  
                sh '''
                docker push prenticed103/flask-jenk:latest
                docker push prenticed103/flask-jenk:dev-v${BUILD_NUMBER}
                '''
               } else {
                sh '''
                echo "Branch not recognised"
                '''
                     }
                     }
            }
        }
        stage('Deploy') {
              steps {
                script {
                if (env.GIT_BRANCH == "origin/main")  {
                sh '''
                kubectl apply -n prod -f ./kubernetes
                kubectl set image deployment/flask-deployment task1=prenticed103/flask-jenk:prod-v${BUILD_NUMBER} -n prod
                '''
            }
                if (env.GIT_BRANCH == "origin/dev")  {
                sh '''
                kubectl apply -n dev -f ./kubernetes
                kubectl set image deployment/flask-deployment task1=prenticed103/flask-jenk:dev-v${BUILD_NUMBER} -n dev
                '''
             } else {
                sh '''
                echo "Branch not recognised"
                '''
                   }
            } 
        }  
        }
        stage('Clean Up') {
            steps 
            {
                sh '''
                docker system prune -f
                '''  
            }
                 }
  
}
}
        
