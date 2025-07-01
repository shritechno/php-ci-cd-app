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
    sshagent(['ubuntu']) {
        sh '''
        # Upload the entire workspace to a temp folder on EC2
        scp -o StrictHostKeyChecking=no -r * ubuntu@13.126.60.153:/home/ubuntu/deploy-temp/

        # Remotely move from temp to web root with sudo
        ssh -o StrictHostKeyChecking=no ubuntu@13.126.60.153 << EOF
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
