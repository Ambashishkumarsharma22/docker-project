pipeline {
    agent any

    environment {
        GIT_SSL_NO_VERIFY = 'true'
    }

    stages {
        stage('Disable Git SSL Verification') {
            steps {
                echo 'Temporarily disabling Git SSL verification...'
                sh 'git config --global http.sslVerify false'
            }
        }

        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t food-delivery-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Running Docker container...'
                sh 'docker run -d -p 8080:8080 --name food-container food-delivery-app'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                // Replace with your Docker Hub credentials setup
                sh '''
                docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                docker tag food-delivery-app $DOCKER_USERNAME/food-delivery-app
                docker push $DOCKER_USERNAME/food-delivery-app
                '''
            }
        }

        stage('Done') {
            steps {
                echo 'Pipeline completed successfully.'
            }
        }
    }
}
