pipeline {
  environment {
    dockerimagename = "drilonnol/react-app"
    dockerImage = ""
    registryCredential = 'dockerhub-credentials'
    tag = "latest"
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/Drilonnol/jankins-kubernetis-deployment.git'
      }
    }
    stage('Build image') {
      steps {
        script {
            dockerImage = docker.build("${dockerimagename}:${tag}", "-f C:\Users\Drilon\Desktop\gitjankins\jankins-kubernetis-deployment\Dockerfile .")
        }
      }
    }
    stage('Pushing Image') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push(tag)
          }
        }
      }
    }
    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yaml,service.yaml")
        }
      }
    }
  }
}
