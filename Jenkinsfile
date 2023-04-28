pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = "docker.io"
        DOCKER_USERNAME = "barrydj"
        DOCKER_PASSWORD = "Md005185++"
    }
    stages {
        stage('Test') {
            steps {
                sh """
                echo 'DOCKER_REGISTRY: ${DOCKER_REGISTRY}'
                echo "DOCKER_USERNAME: ${DOCKER_USERNAME}"
                echo "DOCKER_PASSWORD: ${DOCKER_PASSWORD}"
                """
            }
        }
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '/master']], userRemoteConfigs: [[url: 'https://github.com/barry03/Broker_Kafka.git']]])
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_USERNAME}/http2:${env.BUILD_ID}", "-f Dockerfile .")
                }
            }
        }
        stage('Push Docker image') {
            steps {
                script {
                    docker.withRegistry("${DOCKER_REGISTRY}", "${DOCKER_USERNAME}", "${DOCKER_PASSWORD}") {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_USERNAME}/http2:${env.BUILD_ID}").push()
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
    }
    options {
        disableConcurrentBuilds()
    }
}
