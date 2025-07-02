pipeline {
    agent {
        docker {
            image 'node:18'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/yarlagaddalavanya24/lms-app.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }
        stage('Build App') {
            steps {
                sh 'npm run build'
            }
        }
    }
}

