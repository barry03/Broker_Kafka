pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                withCredentials([string(credentialsId: '2116282e-da84-4b87-9c40-db338284b66e', variable: 'DOCKER_USERNAME'), string(credentialsId: 'admin1', variable: 'DOCKER_PASSWORD'), file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh """
                    echo 'DOCKER_REGISTRY: docker.io'
                    echo "DOCKER_USERNAME: \${DOCKER_USERNAME}"
                    echo "DOCKER_PASSWORD: \${DOCKER_PASSWORD}"
                    echo "KUBECONFIG: \${KUBECONFIG}"
                    """
                }
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
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_USERNAME}/http2:${env.BUILD_ID}", "-f Dockerfile .")
                }
            }
        }
        
        stage('Push Docker image') {
            steps {
                script {
                    docker.withRegistry("${DOCKER_REGISTRY}", 'docker-hub-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_USERNAME}/http2:${env.BUILD_ID}").push()
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubernetesDeploy(
                        kubeconfigId: "${KUBECONFIG}",
                        configs: 'kube/*.yml'
                    )
                }
            }
        }
    }
    options {
        disableConcurrentBuilds()
    }
}
