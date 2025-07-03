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
        sshagent (credentials: ['ec2-key']) {
            sh '''
            rsync -avz --exclude='.git' -e "ssh -o StrictHostKeyChecking=no" ./ ubuntu@13.126.165.208:/home/ubuntu/deploy-temp/

            ssh -o StrictHostKeyChecking=no ubuntu@13.126.165.208 bash -c '
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
