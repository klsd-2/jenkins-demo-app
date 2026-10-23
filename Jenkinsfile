pipeline {
    agent any

    // Check the repo for new commits every minute (works without a public URL)
    triggers {
        pollSCM('* * * * *')
    }

    environment {
        IMAGE_NAME     = 'jenkins-demo-app'
        CONTAINER_NAME = 'jenkins-demo-app'
        APP_PORT       = '3000'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests inside the image...'
                sh 'docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} node --test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:${BUILD_NUMBER}
                    sleep 3
                    docker exec ${CONTAINER_NAME} wget -qO- http://localhost:3000/health
                '''
            }
        }
    }

    post {
        success { echo "Deployed. Open http://localhost:${APP_PORT}" }
        failure { echo 'Pipeline failed - check the stage logs above.' }
    }
}
