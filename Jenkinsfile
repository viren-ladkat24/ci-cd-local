pipeline {
    agent any

    environment {
        IMAGE_NAME = "virenladkat/nginx-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/viren-ladkat24/ci-cd-local.git'
            }
        }

        stage('Build Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%TAG% ."
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat """
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker push %IMAGE_NAME%:%TAG%
                    """
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                bat "kubectl set image deployment/nginx-app nginx-app=%IMAGE_NAME%:%TAG%"
            }
        }
    }
}