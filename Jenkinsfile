pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "esha0629/streamflare"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/esha-dey-22/StreamFlare.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    bat 'echo %PASSWORD% | docker login -u %USERNAME% --password-stdin'
                    bat 'docker push %DOCKER_IMAGE%:latest'
                }
            }
        }

        stage('Run App in Container (Once)') {
            steps {
                bat '''
                docker pull esha0629/streamflare:latest
                docker stop streamflare-container || exit 0
                docker rm streamflare-container || exit 0
                docker run -d -p 3000:80 --name streamflare-container esha0629/streamflare:latest
                '''
            }
        }
    }
}
