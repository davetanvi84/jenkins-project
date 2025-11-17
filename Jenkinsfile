pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "tansdave/node-app"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/davetanvi84/jenkins-project'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }



        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% ."
            }
        }

        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker push %DOCKER_IMAGE%:%BUILD_NUMBER%
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat """
                kubectl set image deployment/node-app node-app=%DOCKER_IMAGE%:%BUILD_NUMBER% --record
                """
            }
        }
    }
}
