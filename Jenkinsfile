pipeline {
    agent any

    environment {
        // Optional: Define NODE_ENV if needed
        NODE_ENV = 'production'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/yarlagaddalavanya24/lms-app.git'
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

    post {
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
}
