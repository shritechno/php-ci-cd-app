pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'MySonarQube' // Change to your actual SonarQube server name in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shritechno/php-ci-cd-app.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv("${env.SONARQUBE_ENV}") {
                    sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=php-ci-cd \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=$SONAR_HOST_URL \
                      -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent (credentials: ['ec2-key']) {
                    sh '''
                    rsync -avz --exclude='.git' -e "ssh -o StrictHostKeyChecking=no" ./ ubuntu@13.200.242.158:/home/ubuntu/deploy-temp/

                    ssh -o StrictHostKeyChecking=no ubuntu@13.200.242.158 bash -c '
                      sudo rm -rf /var/www/html/php-ci-cd-app/*
                      sudo mv /home/ubuntu/deploy-temp/* /var/www/html/php-ci-cd-app/
                      sudo systemctl restart apache2
                    '
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
