pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = "docker.io"
        DOCKER_USERNAME = "barrydj"
        DOCKER_PASSWORD = "Md005185++"
        PATH = "/usr/local/bin:${PATH}"
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
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[url: 'https://gitlab.com/barrydjoulde/patient-zero.git']]])
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    sh "/usr/local/bin/docker build -t ${DOCKER_REGISTRY}/${DOCKER_USERNAME}/http2:${env.BUILD_ID} -f Dockerfile ."
                }
            }
        }
        stage('Push Docker image') {
            steps {
                script {
                    sh "docker login ${DOCKER_REGISTRY} -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD} && docker push ${DOCKER_REGISTRY}/${DOCKER_USERNAME}/http2:${env.BUILD_ID}"
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
