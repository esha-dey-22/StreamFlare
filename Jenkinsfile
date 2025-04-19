pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id') // Jenkins credentials ID
        IMAGE_NAME = 'esha0629/streamflare-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/esha-dey-22/StreamFlare.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t streamflare-app .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                bat 'docker tag streamflare-app %IMAGE_NAME%'
            }
        }

        stage('Login to DockerHub') {
            steps {
                bat "echo %DOCKERHUB_CREDENTIALS_PSW% | docker login -u %DOCKERHUB_CREDENTIALS_USR% --password-stdin"
            }
        }

        stage('Push to DockerHub') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }

        stage('Run App in Container (Once)') {
            steps {
                bat 'docker run -d -p 3000:80 --name streamflare-container %IMAGE_NAME%'
            }
        }
    }
}
