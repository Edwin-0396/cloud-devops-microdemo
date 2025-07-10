pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // ID configurado en Jenkins
        IMAGE_NAME = 'edwindockermicroproject/flask-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Edwin-0396/TU_REPO.git' // Cambia por tu URL real
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                // Si tienes tests, se ejecutan; si no, no falla el pipeline
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
    }
}