pipeline {

    agent any

    environment {

        IMAGE_NAME = "sample-node-app"

        CONTAINER_NAME = "sample-node"

    }

    stages {

        stage('Clone') {
            steps {
                echo "Repository already checked out by Jenkins"
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                docker build \
                -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Deploy') {
            steps {

                sh '''
                docker rm -f ${CONTAINER_NAME} || true

                docker run -d \
                  --name ${CONTAINER_NAME} \
                  -p 3000:3000 \
                  ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Verify') {

            steps {

                sh 'docker ps'

            }

        }

    }

}
