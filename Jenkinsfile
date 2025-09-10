pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube'               // Name in Jenkins SonarQube configuration
        DOCKER_IMAGE = 'myapp-image'          // Docker image name
        APP_NAME = 'myapp'                    // Container name
        DOCKER_SERVER = 'ubuntu@13.60.18.95' // EC2 #3 public IP
        SSH_KEY = '/home/ubuntu/.ssh/id_rsa_docker'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning GitHub repository...'
                git 'https://github.com/PrajwalDataScientist/-Jenkins-CI-CD-Pipeline---SonarQube-Docker-Github-Webhooks-on-AWS.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube scan...'
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner -Dsonar.projectKey=myapp -Dsonar.sources=.'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Deploy to Docker Server') {
            steps {
                echo 'Deploying container via SSH...'
                sh """
                ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ${DOCKER_SERVER} '
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true
                    docker run -d --name ${APP_NAME} -p 80:80 ${DOCKER_IMAGE}
                '
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully ✅'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
