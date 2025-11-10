pipeline {
    agent any

    environment {
      
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'   
        DOCKERHUB_USERNAME = 'sanskrutimhatre42'
        IMAGE_NAME = 'curd-operation'
        CONTAINER_NAME = 'curd-container'
        HOST_PORT = '8081'  
        CONTAINER_PORT = '5000' 
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
                    sh "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    echo '📤 Pushing Docker image to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    echo '🚀 Deploying container...'

                     sh """
                        echo '🧹 Cleaning old containers...'
                        docker ps -q --filter "name=${CONTAINER_NAME}" | xargs -r docker stop
                        docker rm -f ${CONTAINER_NAME} || true
                    """

                  sh """
                        echo '🆕 Starting new container on port ${HOST_PORT}...'
                        docker run -d -p ${HOST_PORT}:${CONTAINER_PORT} --name ${CONTAINER_NAME} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
            echo "🌐 App running at: http://<your-server-ip>:${HOST_PORT}"
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
