pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    cd api
                    docker build -t lms-app .
                '''
            }
        }

        stage('Stop Previous Container') {
            steps {
                sh '''
                    docker stop lms-container || true
                    docker rm lms-container || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                sh '''
                    docker run -d --name lms-container -p 3000:3000 lms-app
                '''
            }
        }
    }
}

