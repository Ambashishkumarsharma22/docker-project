pipeline {
    agent any

    environment {
        IMAGE_NAME = 'food-delivery-app'
        IMAGE_TAG = 'latest'
        CONTAINER_PORT = '3000'
        HOST_PORT = '3000'
    }

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    // Optional: stop and remove existing container
                    sh "docker rm -f ${IMAGE_NAME}-container || true"
                    sh "docker run -d --name ${IMAGE_NAME}-container -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        def dockerImageFullName = "${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                        sh """
                            echo "${DOCKER_PASS}" | docker login --username "${DOCKER_USER}" --password-stdin
                            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${dockerImageFullName}
                            docker push ${dockerImageFullName}
                        """
                    }
                }
            }
        }

        stage('Done') {
            steps {
                echo 'Build, Run and Push complete!'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        cleanup {
            echo 'Cleaning up unused Docker resources...'
            sh 'docker system prune -f'
        }
    }
}

 
