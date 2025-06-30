pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/<your-username>/php-ci-cd-app.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'sonar-scanner -Dsonar.projectKey=php-ci-cd-app -Dsonar.sources=.'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-key']) {
                    sh '''
                    scp index.php ubuntu@<APP_EC2_PUBLIC_IP>:/var/www/html/index.php
                    ssh ubuntu@<APP_EC2_PUBLIC_IP> 'sudo systemctl restart apache2'
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ PHP App Deployed Successfully!'
        }
        failure {
            echo '❌ Build or Deployment Failed!'
        }
    }
}
