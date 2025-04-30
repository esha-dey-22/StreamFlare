pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // Jenkins credentials ID
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
                sh 'docker build -t streamflare-app .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh 'docker tag streamflare-app $IMAGE_NAME'
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Run App in Container (Once)') {
            steps {
                sh 'docker run -d -p 3000:80 --name streamflare-container $IMAGE_NAME'
            }
        }
    }

    post {
        always {
            emailext(
                subject: "📦 Build Result: ${currentBuild.currentResult} - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p><b>Build Status:</b> ${currentBuild.currentResult}</p>
                         <p><b>Job:</b> ${env.JOB_NAME}<br/>
                         <b>Build Number:</b> ${env.BUILD_NUMBER}<br/>
                         <b>URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
                to: 'edey38111@gmail.com',
                mimeType: 'text/html'
            )
        }
    }
}
