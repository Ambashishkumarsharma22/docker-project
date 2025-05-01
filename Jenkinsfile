pipeline {
    agent any

    stages {
        stage('Configure Git SSL') {
            steps {
                // This sets Git to use macOS's system certificates
                sh 'git config --global http.sslCAInfo /etc/ssl/cert.pem'
            }
        }

        stage('Clone') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t food-delivery-app:latest .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh 'docker run -d -p 3000:3000 food-delivery-app:latest'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login --username "$DOCKER_USER" --password-stdin
                        docker tag food-delivery-app:latest "$DOCKER_USER/food-delivery-app:latest"
                        docker push "$DOCKER_USER/food-delivery-app:latest"
                    '''
                }
            }
        }

        stage('Done') {
            steps {
                echo 'Build and Run complete!'
            }
        }
    }
}
