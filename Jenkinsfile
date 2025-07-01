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
                    # Ensure deploy-temp exists
                    ssh -o StrictHostKeyChecking=no ubuntu@13.126.60.153 'mkdir -p /home/ubuntu/deploy-temp'

                    # Upload all files (including dotfiles) to EC2
                    scp -o StrictHostKeyChecking=no -r . ubuntu@13.126.60.153:/home/ubuntu/deploy-temp/

                    # Execute remote deployment commands
                    ssh -o StrictHostKeyChecking=no ubuntu@13.126.60.153 << 'EOF'
                      set -e
                      sudo rm -rf /var/www/html/php-ci-cd-app/*
                      sudo mv /home/ubuntu/deploy-temp/* /var/www/html/php-ci-cd-app/
                      sudo systemctl restart apache2
                    EOF
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
