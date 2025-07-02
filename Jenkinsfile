pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-user/your-lms-app.git'
            }
        }
        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run tests') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }
        stage('Build app') {
            steps {
                sh 'npm run build'
            }
        }
    }
}
