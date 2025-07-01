pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shritechno/php-ci-cd-app.git'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no -r * \
                      ubuntu@13.126.60.153:/var/www/html/php-ci-cd-app/
                    ssh -o StrictHostKeyChecking=no ubuntu@YOUR_EC2_PUBLIC_IP \
                      'sudo systemctl restart apache2'
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
