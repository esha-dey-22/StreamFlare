pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // Jenkins credentials ID
        IMAGE_NAME = 'esha0629/streamflare-app'
        CONTAINER_NAME = 'streamflare-container'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/esha-dey-22/StreamFlare.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push $IMAGE_NAME"
            }
        }

        stage('Logout from DockerHub') {
            steps {
                sh "docker logout"
            }
        }

        stage('Run App in Container') {
            steps {
                script {
                    sh """
                        docker stop $CONTAINER_NAME || true
                        docker rm $CONTAINER_NAME || true
                        docker run -d -p 3000:80 --name $CONTAINER_NAME $IMAGE_NAME
                    """
                }
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
