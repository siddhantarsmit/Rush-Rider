pipeline {
    agent any

    environment {
        DEPLOY_DIR = "/var/www/html"
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
                echo "Validating project structure..."
                sh '''
                ls -l
                test -f index.html || (echo "index.html missing" && exit 1)
                test -f style.css || (echo "style.css missing" && exit 1)
                test -f script.js || (echo "script.js missing" && exit 1)
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying website to Apache..."

                sh """
                sudo rm -rf ${DEPLOY_DIR}/*
                sudo cp -r * ${DEPLOY_DIR}/
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
                body: """
Rush-Rider deployed successfully 🚀

Job      : ${env.JOB_NAME}
Build No : ${env.BUILD_NUMBER}
Repo     : ${env.GIT_URL}
Branch   : ${env.BRANCH_NAME}
URL      : ${env.BUILD_URL}

Website is now live.
"""
            )
        }

        failure {
            emailext(
                to: "${EMAIL}",
                subject: "❌ DEPLOY FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Rush-Rider deployment FAILED ❌

Job      : ${env.JOB_NAME}
Build No : ${env.BUILD_NUMBER}
Repo     : ${env.GIT_URL}
Branch   : ${env.BRANCH_NAME}
URL      : ${env.BUILD_URL}

Fix immediately.
"""
            )
        }
    }
}
