pipeline {
  environment {
    dockerimagename = "wmashal/react-app"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/wmashal/jenkins-kubernetes-deployment.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
   stage('Debug Kubeconfig') {
      steps {
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          sh 'kubectl config view --raw'
        }
      }
    }
    
    stage('Check Cluster') {
      steps {
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          sh 'kubectl --insecure-skip-tls-verify=true cluster-info'
          sh 'kubectl --insecure-skip-tls-verify=true get nodes'
        }
      }
    }
    
    stage('Deploy to Kubernetes') {
      steps {
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          sh 'kubectl --insecure-skip-tls-verify=true apply -f deployment.yaml'
          sh 'kubectl --insecure-skip-tls-verify=true apply -f service.yaml'
        }
      }
    }
  }
}
