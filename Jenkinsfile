pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'MySonarQube' // Your SonarQube server name from Jenkins config
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
        withSonarQubeEnv("${SONARQUBE_ENV}") {
            withCredentials([string(credentialsId: 'sonar-scanner', variable: 'SONAR_AUTH_TOKEN')]) {
                script {
                    def scannerHome = tool 'SonarScanner'
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=php-ci-cd \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=$SONAR_HOST_URL \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                    """
                }
            }
        }
    }
}

        stage('Deploy to EC2') {
            steps {
                sshagent (credentials: ['ec2-key']) {
                    sh '''
                    rsync -avz --exclude='.git' -e "ssh -o StrictHostKeyChecking=no" ./ ubuntu@43.204.112.193:/home/ubuntu/deploy-temp/

                    ssh -o StrictHostKeyChecking=no ubuntu@43.204.112.193 bash -c '
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
