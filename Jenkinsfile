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
                bat 'docker build -t streamflare-app .'
            }
        }

        stage('Run App in Container (Once)') {
            steps {
                bat 'docker run -d -p 3000:80 --name streamflare-container streamflare-app'
            }
        }
    }
}
