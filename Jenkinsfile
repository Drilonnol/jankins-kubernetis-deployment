pipeline {
    environment {
        dockerimagename = "drilonnol/react-app"  // Specify the image name you want to use
        registryCredential = 'dockerhub-credentials'  // Docker Hub credentials stored in Jenkins
        tag = "latest"  // Tag for Docker image
        KUBERNETES_SERVER = 'https://127.0.0.1:61828'  // Kubernetes server URL
    }

    agent any

    stages {
        stage('Checkout Source') {
            steps {
                script {
                git branch: 'main', url: 'https://github.com/Drilonnol/jankins-kubernetis-deployment.git' 
                   
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Push existing Docker image to Docker Hub (no build here)
                    sh '''
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        docker image push $dockerimagename:$tag
                    '''
                }
            }
        }

        stage('Configure Kubernetes') {
            steps {
                script {
                    // Set up Kubernetes configuration using secrets (stored in Jenkins as credentials)
                    withCredentials([string(credentialsId: 'secrets', variable: 'KUBERNETES_TOKEN')]) {
                        // Use environment variable for Kubernetes token securely
                        sh '''
                            kubectl config set-cluster minikube --server=$KUBERNETES_SERVER --insecure-skip-tls-verify=true
                            kubectl config set-credentials minikube --token=$KUBERNETES_TOKEN
                            kubectl config set-context minikube --cluster=minikube --user=minikube
                            kubectl config use-context minikube
                        '''
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Apply Kubernetes deployment and service YAML files
                    sh '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
                }
            }
        }

        stage('Check Deployment Status') {
            steps {
                script {
                    // Wait for the deployment rollout to finish and show the status
                    sh 'kubectl rollout status deployment/react-app-deployment --timeout=2m'
                    
                    // Show the status of pods and services
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'

                    // Retrieve and display the service URL
                    sh 'minikube service react-app-service --url'
                }
            }
        }
    }
}
