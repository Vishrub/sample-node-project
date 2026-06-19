pipeline {

    agent any

    environment {

        IMAGE_NAME = "sample-node-app"
        CONTAINER_NAME = "sample-node"

    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build \
                -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                -t ${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Deploy') {
            steps {

                sh """
                docker rm -f ${CONTAINER_NAME} || true

                docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p 3000:3000 \
                    ${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }

        stage('Verify Deployment') {
            steps {

                sh 'docker ps'

                sh 'docker logs sample-node'

            }
        }

        stage('Cleanup') {
            steps {

                sh '''
                docker image prune -f
                '''

            }
        }

    }

}
