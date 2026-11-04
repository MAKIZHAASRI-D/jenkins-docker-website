pipeline {
    agent any

    environment {
        IMAGE_NAME = 'jenkins-web'
        CONTAINER_NAME = 'my-website'
        HOST_PORT = '8081'
        CONTAINER_PORT = '80'
        REPOSITORY_URL = 'https://github.com/MAKIZHAASRI-D/jenkins-docker-website.git'
    }

    stages {
        stage('Checkout') {
            steps {
                // Fetch the main branch so the build uses the latest website source.
                git branch: 'main', url: "${REPOSITORY_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build an image tagged with this Jenkins run's unique build number.
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Stop Old Container') {
            steps {
                // Stop and remove the previous deployment without failing on first deploy.
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Deploy New Container') {
            steps {
                // Start the new version and publish it on host port 8081.
                sh '''
                    docker run -d \\
                        --name ${CONTAINER_NAME} \\
                        -p ${HOST_PORT}:${CONTAINER_PORT} \\
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                // Confirm the container is running and the website responds successfully.
                sh '''
                    docker ps
                    curl -f --silent --show-error http://host.docker.internal:8081
                    echo 'Website verification succeeded.'
                '''
            }
        }
    }

    post {
        success {
            echo 'Jenkins CI/CD deployment completed successfully.'
        }
        failure {
            echo 'Jenkins CI/CD deployment failed.'
        }
    }
}
