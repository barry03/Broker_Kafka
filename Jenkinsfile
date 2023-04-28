https://github.com/barry03/Broker_Kafka.git

pipeline {

  environment {
    dockerimagename = "test/http2"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/barry03/Broker_Kafka.git'
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

    stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        kubernetesDeploy(
                            kubeconfigId: "${KUBECONFIG}",
                            configs: 'kube/.yml'
                        )
                    }
                }
            }
        }
    options {
        disableConcurrentBuilds()
    }

}
