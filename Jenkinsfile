pipeline {
    agent any

    environment {
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
                sh 'pwd'
                sh 'ls -l'

                // Enter project folder
                sh 'cd RushRider && ls -l'

                // Validate required files exist
                sh 'cd RushRider && test -f index.html'
                sh 'cd RushRider && test -f style.css'
                sh 'cd RushRider && test -f script.js'
            }
        }

        stage('Deploy') {
            steps {
                // Clean old site
                sh 'sudo rm -rf /var/www/html/*'

                // Deploy only website files (NOT PDFs, Excel, etc.)
                sh 'sudo cp RushRider/index.html /var/www/html/'
                sh 'sudo cp RushRider/style.css /var/www/html/'
                sh 'sudo cp RushRider/script.js /var/www/html/'
                sh 'sudo cp -r RushRider/img /var/www/html/'

                // Fix permissions
                sh 'sudo chown -R www-data:www-data /var/www/html'
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
Branch   : ${env.BRANCH_NAME}
URL      : ${env.BUILD_URL}

Website is now LIVE.
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
Branch   : ${env.BRANCH_NAME}
URL      : ${env.BUILD_URL}

Fix immediately.
"""
            )
        }
    }
}
