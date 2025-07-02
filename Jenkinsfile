pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDS = 'dockerhub-creds'
        FRONTEND_IMAGE = 'lavanyayarlagadda24/webapp'
        BACKEND_IMAGE = 'lavanyayarlagadda24/api'
        POSTGRES_CONTAINER = 'postgres_container'
        POSTGRES_USER = 'myuser'
        POSTGRES_PASSWORD = 'mypass'
        POSTGRES_DB = 'mydb'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Start PostgreSQL') {
            steps {
                echo 'Starting PostgreSQL container...'
                sh """
                    docker run -d \
                      --name $POSTGRES_CONTAINER \
                      -e POSTGRES_USER=$POSTGRES_USER \
                      -e POSTGRES_PASSWORD=$POSTGRES_PASSWORD \
                      -e POSTGRES_DB=$POSTGRES_DB \
                      -p 5432:5432 \
                      postgres:15
                """
                sleep(time: 10, unit: 'SECONDS') // give DB time to initialize
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('webapp') {
                    sh "docker build -t $FRONTEND_IMAGE ."
                }
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('api') {
                    sh """
                        docker build -t $BACKEND_IMAGE \
                          --build-arg DB_HOST=host.docker.internal \
                          --build-arg DB_USER=$POSTGRES_USER \
                          --build-arg DB_PASSWORD=$POSTGRES_PASSWORD \
                          --build-arg DB_NAME=$POSTGRES_DB .
                    """
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $FRONTEND_IMAGE:latest
                        docker push $BACKEND_IMAGE:latest
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker rm -f $POSTGRES_CONTAINER || true'
        }
    }
}

