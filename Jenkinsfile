pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"
        IMAGE_NAME = "ashish7840/web-cd"
        REGISTRY_CREDENTIAL = "dockerhub-creds"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ashish836826/web-cd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY}", "${REGISTRY_CREDENTIAL}") {
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        always {
            bat """
            docker rmi ${IMAGE_NAME}:${env.BUILD_NUMBER}
            docker rmi ${IMAGE_NAME}:latest
            exit 0
            """
        }

        success {
            echo "CI/CD pipeline finished successfully!"
        }

        failure {
            echo "CI/CD pipeline failed."
        }
    }
}
