pipeline {
    agent any

    environment {
        DOCKER_EC2_IP = '13.60.18.95'   // Replace with your Docker EC2 public IP
        SSH_CREDENTIALS_ID = 'docker-ec2-key' // Jenkins SSH credentials ID
        DOCKER_IMAGE = 'myapp-image'
    }

    stages {
        stage('Checkout') {
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

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Deploy to Docker Server') {
            steps {
                sshagent([SSH_CREDENTIALS_ID]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${DOCKER_EC2_IP} '
                        docker stop ${DOCKER_IMAGE} || true
                        docker rm ${DOCKER_IMAGE} || true
                        docker rmi ${DOCKER_IMAGE} || true
                        exit
                        '
                    """
                    sh """
                        scp -o StrictHostKeyChecking=no -r ./ ubuntu@${DOCKER_EC2_IP}:~/myapp/
                    """
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${DOCKER_EC2_IP} '
                        cd ~/myapp
                        docker build -t ${DOCKER_IMAGE} .
                        docker run -d --name ${DOCKER_IMAGE} -p 80:80 ${DOCKER_IMAGE}
                        '
                    """
                }
            }
        }
    }
}
