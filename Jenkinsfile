pipeline {
    agent any

    environment {
        IMAGE_NAME = "rushrider"
        EMAIL = "siddhantarsmit@gmail.com"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                sh 'cd RushRider && test -f index.html'
                sh 'cd RushRider && test -f style.css'
                sh 'cd RushRider && test -f script.js'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Run Container (Test)') {
            steps {
                sh 'docker rm -f rush-test || true'
                sh 'docker run -d -p 8081:80 --name rush-test ${IMAGE_NAME}:latest'
            }
        }
    }

    post {
        success {
            emailext(
                to: "${EMAIL}",
                subject: "✅ DOCKER BUILD SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Docker image built and container running on port 8081"
            )
        }

        failure {
            emailext(
                to: "${EMAIL}",
                subject: "❌ DOCKER BUILD FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Docker build failed. Check Jenkins console."
            )
        }
    }
}
