pipeline {
    environment {
        registryCredential = 'dockerhub-credentials'
        dockerImage = ''
        registry = 'registry.hub.docker.com'
        DOCKER_USERNAME = credentials('barrydj').USAGER.toString()
        DOCKER_PASSWORD = credentials('barrydj').getPassword().toString()

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
                    dockerImage = docker.build "${registry}/test/http2"
                }
            }
        }
        stage('Pushing Image') {
            steps{
                script {
                    docker.withRegistry( registry, registryCredential ) {
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
                            configs: 'kube/*.yml'
                        )
                    }
                }
            }
        }
    }
    options {
        disableConcurrentBuilds()
    }
}
