pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "rnitintech0712/vote:v${BUILD_NUMBER}"
        KUBECONFIG = "$HOME/.kube/config" // Ensure that KUBECONFIG is set properly
    }
    stages {
        stage('Install Minikube') {
            steps {
                sh '''
                # Install Minikube if not already installed
                if ! minikube version; then
                    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
                    chmod +x minikube
                    sudo mv minikube /usr/local/bin/
                fi
                
                # Verify Minikube installation
                minikube version
                '''
            }
        }
        stage('Start Minikube') {
            steps {
                sh '''
                # Start Minikube if it's not running
                minikube status || minikube start --driver=docker
                
                # Set Docker environment to use Minikube's Docker daemon
                eval $(minikube docker-env)

                # Ensure kubectl is set to use the Minikube context
                kubectl config use-context minikube
                '''
            }
        }
        stage('BUILDING DOCKER IMAGE') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                    cd vote
                    docker build -t $DOCKER_IMAGE .
                    echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin
                    docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
        stage('Install kubectl') {
            steps {
                sh '''
                if ! kubectl version --client; then
                    curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
                    chmod +x ./kubectl
                    sudo mv ./kubectl /usr/local/bin/kubectl || mv ./kubectl /usr/local/bin/kubectl
                fi
                '''
            }
        }
        stage('DEPLOY TO MINIKUBE') {
            steps {
                script {
                    sh '''
                    # Deploy the new Docker image to the Kubernetes cluster
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
