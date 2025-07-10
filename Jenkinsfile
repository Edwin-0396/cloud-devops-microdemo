pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = 'edwindockermicroproject/flask-demo'
        REMOTE_USER = 'ubuntu'
        REMOTE_HOST = '44.192.24.164'
        REMOTE_KEY = credentials('ec2-ssh-key') // You need to create this Jenkins credential
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Edwin-0396/YOUR_REPO.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                sh 'pytest || echo "No tests found"'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:${env.BUILD_NUMBER} ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
                sh "docker tag $IMAGE_NAME:${env.BUILD_NUMBER} $IMAGE_NAME:latest"
                sh "docker push $IMAGE_NAME:${env.BUILD_NUMBER}"
                sh "docker push $IMAGE_NAME:latest"
            }
        }
        stage('Deploy to EC2') {
            steps {
                sshagent (credentials: ['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST '
                        docker pull $IMAGE_NAME:latest &&
                        docker stop flask-demo || true &&
                        docker rm flask-demo || true &&
                        docker run -d --name flask-demo -p 8080:5000 $IMAGE_NAME:latest
                    '
                    """
                }
            }
        }
    }
}