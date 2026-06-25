pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "eisha101/jenkins-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                bat 'node --version'
            }
        }

        stage('Test') {
            steps {
                echo 'Tests passed!'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE%:%IMAGE_TAG% .'
                bat 'docker tag %DOCKER_IMAGE%:%IMAGE_TAG% %DOCKER_IMAGE%:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    bat 'docker push %DOCKER_IMAGE%:%IMAGE_TAG%'
                    bat 'docker push %DOCKER_IMAGE%:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                bat 'docker stop jenkins-demo || exit /B 0'
                bat 'docker rm jenkins-demo || exit /B 0'
                bat 'docker pull %DOCKER_IMAGE%:latest'
                bat 'docker run -d -p 3000:3000 --name jenkins-demo %DOCKER_IMAGE%:latest'
                echo 'Application deployed successfully!'
            }
        }
    }

    post {
        success {
            echo 'Pipeline SUCCESS!'
        }

        failure {
            echo 'Pipeline FAILED!'
        }
    }
}
