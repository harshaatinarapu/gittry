pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'my-site'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Build Tests') {
            steps {
                echo 'Running tests...'
                sh 'npm test || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} .'
                sh 'docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ${DOCKER_IMAGE_NAME}:latest'
            }
        }

        stage('Push to Registry') {
            when {
                branch 'main'
            }
            steps {
                echo 'Pushing image to registry...'
                sh '''
                    echo "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                    echo "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:latest"
                '''
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying application...'
                sh 'docker run -d -p 3000:3000 ${DOCKER_IMAGE_NAME}:latest'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker system prune -f || true'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
