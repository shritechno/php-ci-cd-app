pipeline {
    agent any

    tools {
        // Load the SonarScanner tool installed in Jenkins (must match the name you gave it in Global Tool Config)
        sonarRunner 'SonarScanner'
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token') // or however you set your credentials
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/shritechno/php-ci-cd-app.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarScanner') {
                    sh """
                        ${tool 'SonarScanner'}/bin/sonar-scanner \
                        -Dsonar.projectKey=php-ci-cd-app \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://13.201.26.22:9000/ \
                        -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                echo 'Deploying to EC2...'
                // Your deploy steps here
            }
        }
    }

    post {
        failure {
            echo '❌ Build or Deployment Failed!'
        }
    }
}
