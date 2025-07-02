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

        stage('Clean Up Old Containers') {
            steps {
                echo 'Stopping and removing old containers if they exist...'
                sh '''
                    docker rm -f frontend || true
                    docker rm -f backend || true
                    docker rm -f postgres_container || true
                '''
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
                sleep(time: 10, unit: 'SECONDS')
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

        stage('Build Frontend Image') {
            steps {
                dir('webapp') {
                    sh "docker build -t $FRONTEND_IMAGE ."
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$DOCKER_HUB_CREDS", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $BACKEND_IMAGE
                        docker push $FRONTEND_IMAGE
                    """
                }
            }
        }

        stage('Run Backend Container') {
            steps {
                sh """
                    docker run -d --name backend \
                      --link $POSTGRES_CONTAINER:db \
                      -e DB_HOST=db \
                      -e DB_USER=$POSTGRES_USER \
                      -e DB_PASSWORD=$POSTGRES_PASSWORD \
                      -e DB_NAME=$POSTGRES_DB \
                      -p 3000:3000 $BACKEND_IMAGE
                """
            }
        }

        stage('Run Frontend Container') {
            steps {
                sh """
                    docker run -d --name frontend \
                      -p 80:80 $FRONTEND_IMAGE
                """
            }
        }
    }
}

