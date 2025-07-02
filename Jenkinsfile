pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'SonarScanner' // Matches Jenkins → Configure System → SonarQube
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
                        withEnv(["PATH+SONAR=${tool 'SonarScanner'}/bin"]) {
                            sh '''
                                sonar-scanner \
                                  -Dsonar.projectKey=php-ci-cd-app \
                                  -Dsonar.sources=. \
                                  -Dsonar.language=php \
                                  -Dsonar.host.url=http://13.201.26.22:9000/ \
                                  -Dsonar.login=$SONAR_TOKEN
                            '''
                        }
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
