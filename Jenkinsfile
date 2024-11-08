pipeline {
  environment {
    dockerimagename = "drilonnol/react-app"
    registryCredential = 'dockerhub-credentials'
    tag = "latest"
    KUBERNETES_SERVER = 'https://<your-kubernetes-cluster-url>'
    KUBERNETES_TOKEN = credentials('k8s-token') // Kjo është një credential për tokenin
    KUBERNETES_CA_CERT = credentials('k8s-ca-cert') // Kjo është një credential për certifikatën
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/Drilonnol/jankins-kubernetis-deployment.git'
      }
    }
    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          // Konfigurojmë kubectl pa kubeconfig
          sh """
            echo "\$KUBERNETES_CA_CERT" > /tmp/ca.crt
            kubectl config set-cluster my-cluster --server=\$KUBERNETES_SERVER --certificate-authority=/tmp/ca.crt
            kubectl config set-credentials user --token=\$KUBERNETES_TOKEN
            kubectl config set-context my-context --cluster=my-cluster --user=user
            kubectl config use-context my-context
          """
          // Aplikojmë konfigurimet për Kubernetes
          sh 'kubectl apply -f deployment.yaml'
          sh 'kubectl apply -f service.yaml'
        }
      }
    }
  }
}
