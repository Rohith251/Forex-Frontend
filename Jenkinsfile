pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rohith0702/forex-frontend'
        DOCKER_TAG = 'latest'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
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
                echo 'Installing Node modules...'
                bat 'npm install'
            }
        }

        stage('Build') {
            steps {
                echo 'Building frontend...'
                bat 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker push %DOCKER_IMAGE%:%DOCKER_TAG%
                    '''
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo 'Deploying frontend using Docker Compose...'
                bat 'docker-compose -f docker-compose.frontend.yml down || exit 0'
                bat 'docker-compose -f docker-compose.frontend.yml up -d'
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Frontend build and deploy succeeded.'
        }
        failure {
            echo 'Frontend build or deploy failed.'
        }
    }
}
