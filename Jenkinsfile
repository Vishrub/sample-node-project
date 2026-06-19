pipeline {

    agent any

    environment {
        IMAGE_NAME = "sample-node-app"
        CONTAINER_NAME = "sample-node"
        APP_PORT = "3000"
    }

    options {
        timestamps()
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Node Version:"
                    node -v

                    echo "NPM Version:"
                    npm -v

                    npm ci
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    npm test
                '''
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

        stage('Deploy Application') {
            steps {
                sh """
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:3000 \
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "Running Containers:"
                    docker ps

                    echo ""
                    echo "Application Logs:"
                    docker logs sample-node
                '''
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

    post {

        success {
            echo "Build #${BUILD_NUMBER} deployed successfully."
        }

        failure {
            echo "Build failed."

            sh '''
                echo "Running containers:"
                docker ps -a
            '''
        }

        always {
            cleanWs()
        }
    }
}
