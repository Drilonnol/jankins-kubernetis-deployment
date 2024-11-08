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
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    docker.build(dockerimagename, "-t $dockerimagename:$tag .")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    docker.withRegistry('', registryCredential) {
                        docker.image(dockerimagename).push("$tag")
                    }
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
                }
            }
        }
    }
}
