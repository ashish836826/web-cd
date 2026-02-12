pipeline {
    agent any

    environment {
        DOCKER_HUB = "ashish7840"
        IMAGE_NAME = "my-node-app"
        IMAGE_TAG = "82"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ashish836826/web-cd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                docker build -t %DOCKER_HUB%/%IMAGE_NAME%:%IMAGE_TAG% .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat """
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat """
                docker push %DOCKER_HUB%/%IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Deploy Container') {
            steps {
                bat """
                docker stop my-container || exit 0
                docker rm my-container || exit 0
                docker run -d -p 3000:3000 --name my-container %DOCKER_HUB%/%IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }
    }

    post {
        always {
            bat "docker logout"
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
