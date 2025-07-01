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
                ssh -o StrictHostKeyChecking=no ubuntu@13.126.60.153 'mkdir -p /home/ubuntu/deploy-temp'
                tar --exclude='.git' -czf app.tar.gz .
                scp -o StrictHostKeyChecking=no app.tar.gz ubuntu@13.126.60.153:/home/ubuntu/deploy-temp/
                ssh -o StrictHostKeyChecking=no ubuntu@13.126.60.153 'cd /home/ubuntu/deploy-temp && tar -xzf app.tar.gz'
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
