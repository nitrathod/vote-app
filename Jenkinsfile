pipeline{
    agent any
    stages{
        stage('BUILDING DOCKER IMAGE'){
            steps {
                sh ''' 
                cd vote
                docker build -t rnitintech0712/vote:v${BUILD_NUMBER} .
                docker login --username=rnitintech0712 --password=Nitin71990#
                docker push rnitintech0712/vote:v${BUILD_NUMBER}
                '''
            }
        }
        stage('DEPLOY TO MINIKUBE') {
            steps {
                script{
                    sh '''
                    kubectl set image deployment/vote vote=rnitintech0712/vote:v${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
}    
