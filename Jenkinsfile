pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDS = 'dockerhub-creds'
        FRONTEND_IMAGE = 'lavanyayarlagadda24/webapp'
        BACKEND_IMAGE = 'lavanyayarlagadda24/api'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('webapp') {
                    sh 'docker build -t $FRONTEND_IMAGE .'
                }
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('api') {
                    sh 'docker build -t $BACKEND_IMAGE .'
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: "$DOCKER_HUB_CREDS", url: '']) {
                    sh 'docker push $FRONTEND_IMAGE:latest'
                    sh 'docker push $BACKEND_IMAGE:latest'
                }
            }
        }
    }
}
