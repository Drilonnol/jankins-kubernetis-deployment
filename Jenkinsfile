pipeline {
    environment {
        dockerimagename = "drilonnol/react-app"
        registryCredential = 'dockerhub-credentials'
        tag = "latest"
        KUBERNETES_SERVER = 'https://127.0.0.1:61828'
        KUBERNETES_TOKEN = credentials('secrets') // Token stored in Jenkins credentials
    }
    agent any
    stages {
        stage('Checkout Source') {
            steps {
                git branch: 'main', url: 'https://github.com/Drilonnol/jankins-kubernetis-deployment.git'
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Ensure Minikube's Docker environment is correctly configured
                    sh '''
                        eval $(minikube -p minikube docker-env)
                        docker build -t ${dockerimagename}:${tag} .
                        docker push ${dockerimagename}:${tag}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Configure kubectl to use your Minikube cluster
                    withEnv(["KUBERNETES_TOKEN=${KUBERNETES_TOKEN}", "KUBERNETES_SERVER=${KUBERNETES_SERVER}"]) {
                        sh '''
                            kubectl config set-cluster minikube --server=${KUBERNETES_SERVER} --insecure-skip-tls-verify=true
                            kubectl config set-credentials minikube --token=${KUBERNETES_TOKEN}
                            kubectl config set-context minikube --cluster=minikube --user=minikube
                            kubectl config use-context minikube
                        '''
                    }
                    
                    // Apply deployment and service YAML files
                    sh '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''

                    // Wait for the pods to be ready before proceeding
                    sh '''
                        kubectl rollout status deployment/react-app-deployment --timeout=2m
                    '''

                    // Check the deployment status
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'

                    // Display the URL of the service via minikube
                    sh '''
                        minikube service react-app-service --url
                    '''
                }
            }
        }
    }
}
