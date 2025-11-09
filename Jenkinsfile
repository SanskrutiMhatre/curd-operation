pipeline {
    agent any

    environment {
        // 👇 Replace these with your actual Docker Hub credentials & repo
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'   // Jenkins credentials ID
        DOCKERHUB_USERNAME = 'sanskrutimhatre42'
        IMAGE_NAME = 'curd-operation'
        CONTAINER_NAME = 'curd-container'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo '🐳 Building Docker image...'
                    bat "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    echo '📤 Pushing Docker image to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        bat "echo $PASS | docker login -u $USER --password-stdin"
                        bat "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    echo '🚀 Deploying container...'
                    // Stop and remove existing container (if any)
                    bat "docker rm -f ${CONTAINER_NAME} || true"

                    // Run the new container
                    sh "docker run -d -p 8081:5000 --name ${CONTAINER_NAME} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
                    bat "docker run -d -p 8080:5000 --name ${CONTAINER_NAME} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
