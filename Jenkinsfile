pipeline {
    agent any
    
    environment {
        SONARQUBE_ENV = 'SonarScanner' // This must match Jenkins > Manage Jenkins > Configure System > SonarQube installation name
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shritechno/php-ci-cd-app.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${env.SONARQUBE_ENV}") {
                    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                        sh '''
                            sonar-scanner \
                              -Dsonar.projectKey=php-ci-cd-app \
                              -Dsonar.sources=. \
                              -Dsonar.exclusions=**/vendor/**,**/.git/**,**/*.min.js \
                              -Dsonar.language=php \
                              -Dsonar.token=$SONAR_TOKEN \
                              -Dsonar.host.url=http://13.201.26.22:9000/
                        '''
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent (credentials: ['ec2-key']) {
                    sh '''
                        rsync -avz --exclude='.git' -e "ssh -o StrictHostKeyChecking=no" ./ ubuntu@13.126.60.153:/home/ubuntu/deploy-temp/

                        ssh -o StrictHostKeyChecking=no ubuntu@13.126.60.153 bash -c "'
                          sudo rm -rf /var/www/html/php-ci-cd-app/*
                          sudo mv /home/ubuntu/deploy-temp/* /var/www/html/php-ci-cd-app/
                          sudo systemctl restart apache2
                        '"
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
