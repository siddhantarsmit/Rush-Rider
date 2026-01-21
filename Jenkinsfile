pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ACCOUNT_ID = "087471322416"
        REPO_NAME  = "rushrider"
        IMAGE_TAG  = "latest"
        EMAIL      = "siddhantarsmit@gmail.com"

        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}"
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
                sh 'docker build -t ${REPO_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region ${AWS_REGION} \
                | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                '''
            }
        }

        stage('Tag Image for ECR') {
            steps {
                sh 'docker tag ${REPO_NAME}:${IMAGE_TAG} ${ECR_REPO}:${IMAGE_TAG}'
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh 'docker push ${ECR_REPO}:${IMAGE_TAG}'
            }
        }
    }

    post {
        success {
            emailext(
                to: "${EMAIL}",
                subject: "✅ ECR PUSH SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Docker image pushed successfully to Amazon ECR 🚀

Repository : ${ECR_REPO}
Tag        : ${IMAGE_TAG}

Job   : ${env.JOB_NAME}
Build : ${env.BUILD_NUMBER}
URL   : ${env.BUILD_URL}
"""
            )
        }

        failure {
            emailext(
                to: "${EMAIL}",
                subject: "❌ ECR PUSH FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Docker build or push FAILED ❌

Job   : ${env.JOB_NAME}
Build : ${env.BUILD_NUMBER}
URL   : ${env.BUILD_URL}

Check Jenkins console immediately.
"""
            )
        }
    }
}
