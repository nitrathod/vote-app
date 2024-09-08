pipeline{
    agent any
    stages{
        stage('BUILDING DOCKER IMAGE'){
            steps {
                sh ''' 
                cd vote
                docker build -t rnitintech0712/vote:${BUILD_NUMBER} .
                docker login --username=rnitintech0712 --password=Nitin71990#
                docker push rnitintech0712/vote:v1
                '''
            }
        }
        stage('DEPLOY TO MINIKUBE') {
            steps {
                script{
                    sh '''
                    minikube start
                    kubectl set image deployment/vote vote=rnitintech0712/vote:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
}    
