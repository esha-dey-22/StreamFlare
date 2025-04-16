pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/esha-dey-22/StreamFlare.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('streamflare-app')
                }
            }
        }

        stage('Run App in Container') {
            steps {
                script {
                    docker.image('streamflare-app').run('-p 8080:80')
                }
            }
        }
    }
}
