pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/PrajwalDataScientist/-Jenkins-CI-CD-Pipeline---SonarQube-Docker-Github-Webhooks-on-AWS.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner -Dsonar.projectKey=git-pipeline -Dsonar.sources=.'
                }
            }
        }

        stage('Deploy to Docker EC2') {
            steps {
                sshagent(['docker-ec2-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.60.18.95 "
                    cd ~/myapp || mkdir -p ~/myapp
                    git pull origin main
                    docker stop myapp || true
                    docker rm myapp || true
                    docker build -t myapp-image .
                    docker run -d --name myapp -p 80:80 myapp-image
                    "
                    '''
                }
            }
        }
    }
}
