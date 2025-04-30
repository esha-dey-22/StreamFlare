pipeline {
    agent any

    environment {
        IMAGE_NAME = "streamflare"
        DOCKER_HUB_USERNAME = "esha0629"
        DOCKER_HUB_REPO = "${DOCKER_HUB_USERNAME}/${IMAGE_NAME}"
        DOCKER_HUB_CREDENTIALS_ID = "dockerhub"  // Make sure this exists in Jenkins Credentials
        VERSION = "v${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/esha-dey-22/StreamFlare.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Clean Old Container') {
            steps {
                sh 'docker rm -f streamflare_container || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${DOCKER_HUB_REPO}:latest -t ${DOCKER_HUB_REPO}:${VERSION} .
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh """
                    docker push ${DOCKER_HUB_REPO}:latest
                    docker push ${DOCKER_HUB_REPO}:${VERSION}
                """
            }
        }

        stage('Run New Container') {
            steps {
                sh """
                    docker run -d -p 8080:80 --name streamflare_container ${DOCKER_HUB_REPO}:${VERSION}
                """
            }
        }

        stage('Cleanup Dangling Images (Optional)') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }

    post {
        success {
            emailext (
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Jenkins job <b>${env.JOB_NAME}</b> was successful!</p>
                    <p><b>Docker Image:</b> ${DOCKER_HUB_REPO}:${VERSION}</p>
                    <p>URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: 'your-email@example.com'
            )
        }
        failure {
            emailext (
                subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Jenkins job <b>${env.JOB_NAME}</b> failed.</p>
                    <p>Check the logs: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: 'your-email@example.com'
            )
        }
    }
}
