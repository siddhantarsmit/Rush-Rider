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
                sh 'cd RushRider && ls -l'
                sh 'cd RushRider && test -f index.html'
                sh 'cd RushRider && test -f style.css'
                sh 'cd RushRider && test -f script.js'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'sudo rm -rf /var/www/html/*'
                sh 'sudo cp RushRider/index.html /var/www/html/'
                sh 'sudo cp RushRider/style.css /var/www/html/'
                sh 'sudo cp RushRider/script.js /var/www/html/'
                sh 'sudo cp -r RushRider/img /var/www/html/'
                sh 'sudo chown -R www-data:www-data /var/www/html'
            }
        }

    post {
        success {
            emailext(
                to: "${EMAIL}",
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Rush-Rider deployed successfully."
            )
        }

        failure {
            emailext(
                to: "${EMAIL}",
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Rush-Rider deployment failed. Check Jenkins console."
            )
        }
    }
}
