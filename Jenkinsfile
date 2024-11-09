pipeline {
    environment {
        dockerimagename = "drilonnol/react-app"
        registryCredential = 'dockerhub-credentials'
        tag = "latest"
        KUBERNETES_SERVER = 'https://127.0.0.1:61828'
    }
    agent any
    stages {
        stage('Checkout Source') {
            steps {
                bat '''
                    git clone -b main https://github.com/Drilonnol/jankins-kubernetis-deployment.git
                '''
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                bat '''
                    @echo off
                    minikube -p minikube docker-env --shell=cmd | call
                    docker build -t %dockerimagename%:%tag% .
                    docker push %dockerimagename%:%tag%
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([string(credentialsId: 'secrets', variable: 'KUBERNETES_TOKEN')]) {
                    bat '''
                        kubectl config set-cluster minikube --server=%KUBERNETES_SERVER% --insecure-skip-tls-verify=true
                        kubectl config set-credentials minikube --token=%KUBERNETES_TOKEN%
                        kubectl config set-context minikube --cluster=minikube --user=minikube
                        kubectl config use-context minikube
                    '''

                    bat '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
                    
                    bat '''
                        kubectl rollout status deployment/react-app-deployment --timeout=2m
                    '''

                    bat 'kubectl get pods'
                    bat 'kubectl get svc'

                    bat '''
                        minikube service react-app-service --url
                    '''
                }
            }
        }
    }
}
