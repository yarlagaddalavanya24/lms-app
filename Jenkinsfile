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
                script {
                    sh """
                        docker rm -f $POSTGRES_CONTAINER || true
                        docker run -d --name $POSTGRES_CONTAINER \
                          -e POSTGRES_USER=$POSTGRES_USER \
                          -e POSTGRES_PASSWORD=$POSTGRES_PASSWORD \
                          -e POSTGRES_DB=$POSTGRES_DB \
                          -p 5432:5432 postgres:15
                    """
                    sleep 10 // wait for DB to initialize
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir('api') {
                    sh """
                        docker build -t $BACKEND_IMAGE \
                          --build-arg DB_HOST=db \
                          --build-arg DB_USER=$POSTGRES_USER \
                          --build-arg DB_PASSWORD=$POSTGRES_PASSWORD \
                          --build-arg DB_NAME=$POSTGRES_DB .
                    """
                }
            }
        }

        stage('Run Backend') {
            steps {
                sh """
                    docker rm -f backend || true
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

        stage('Build Frontend') {
            steps {
                dir('webapp') {
                    sh "docker build -t $FRONTEND_IMAGE ."
                }
            }
        }

        stage('Run Frontend') {
            steps {
                sh """
                    docker rm -f frontend || true
                    docker run -d --name frontend \
                      -p 80:80 $FRONTEND_IMAGE
                """
            }
        }
    }
}

