pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "rnitintech0712/vote:v${BUILD_NUMBER}"
        KUBECONFIG = "$HOME/.kube/config" // Ensure that KUBECONFIG is set properly
    }
    stages {
        stage('Start Minikube') {
            steps {
                sh '''
                # Start Minikube if it's not running
                minikube status || minikube start
                
                # Ensure kubectl is set to use the Minikube context
                kubectl config use-context minikube
                '''
            }
        }
        stage('BUILDING DOCKER IMAGE') {
            steps {
                sh '''
                cd vote
                docker build -t $DOCKER_IMAGE .
                docker login --username=rnitintech0712 --password=Nitin71990#
                docker push $DOCKER_IMAGE
                '''
                }
            }
        }
        stage('Install kubectl') {
            steps {
                sh '''
                curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
                chmod +x ./kubectl
                sudo mv ./kubectl /usr/local/bin/kubectl || mv ./kubectl /usr/local/bin/kubectl
                '''
            }
        }
        stage('DEPLOY TO MINIKUBE') {
            steps {
                script {
                    sh '''
                    kubectl set image deployment/vote vote=$DOCKER_IMAGE
                    '''
                }
            }
        }
    }
    post {
        always {
            sh '''
            # Stop Minikube after deployment (optional)
            minikube stop
            '''
        }
    }
}
