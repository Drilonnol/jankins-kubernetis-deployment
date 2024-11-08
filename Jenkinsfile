pipeline {
    environment {
        dockerimagename = "drilonnol/react-app"
        registryCredential = 'dockerhub-credentials'
        tag = "latest"
        KUBERNETES_SERVER = 'https://127.0.0.1:61828'
        KUBERNETES_TOKEN = credentials('secrets') // use the token stored in Jenkins credentials
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
                    // Use Minikube's Docker environment
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
                    // Configure kubectl to use your Minikube cluster without the certificate
                    withEnv(["KUBERNETES_TOKEN=${KUBERNETES_TOKEN}", "KUBERNETES_SERVER=${KUBERNETES_SERVER}"]) {
                        sh '''
                            kubectl config set-cluster minikube --server=${KUBERNETES_SERVER} --insecure-skip-tls-verify=true
                            kubectl config set-credentials minikube --token=${KUBERNETES_TOKEN}
                            kubectl config set-context minikube --cluster=minikube --user=minikube
                            kubectl config use-context minikube
                        '''
                    }
                    
                    // Apply the deployment and service YAML files
                    sh '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
                    
                    // Check the deployment status
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'

                    // Get the service details and display in Jenkins logs
                    sh '''
                        minikube service react-app-service --url
                    '''
                }
            }
        }
    }
}
