pipeline {
    agent any

    environment {
        PROJECT_DIR = "RushRider"
        DEPLOY_DIR  = "/var/www/html"
        EMAIL       = "siddhantarsmit@gmail.com"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                sh """
                echo "Workspace:"
                pwd
                ls -l

                echo "Entering project folder..."
                cd ${PROJECT_DIR}

                ls -l
                test -f index.html || (echo "index.html missing" && exit 1)
                test -f style.css || (echo "style.css missing" && exit 1)
                test -f script.js || (echo "script.js missing" && exit 1)
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                echo "Deploying to Apache..."

                sudo rm -rf ${DEPLOY_DIR}/*
                sudo cp -r ${PROJECT_DIR}/* ${DEPLOY_DIR}/
                sudo chown -R www-data:www-data ${DEPLOY_DIR}
                """
            }
        }
    }

    post {
        success {
            emailext(
                to: "${EMAIL}",
                subject: "✅ DEPLOY SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Rush-Rider deployed successfully."
            )
        }

        failure {
