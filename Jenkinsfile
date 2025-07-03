pipeline {
    agent any

    environment {
        DB_IMAGE = 'custom-postgres:latest'
        DB_CONTAINER = 'postgres_container'
        FRONTEND_IMAGE = 'lavanyayarlagadda24/webapp:latest'
        BACKEND_IMAGE = 'lavanyayarlagadda24/api:latest'
        FRONTEND_CONTAINER = 'frontend'
        BACKEND_CONTAINER = 'backend'
        NETWORK_NAME = 'lms-net'
    }

    stages {
        stage('Build PostgreSQL Image') {
            steps {
                dir('postgres') {
                    sh 'docker build -t $DB_IMAGE .'
                }
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                sh '''
                docker rm -f $DB_CONTAINER || true
                docker rm -f $FRONTEND_CONTAINER || true
                docker rm -f $BACKEND_CONTAINER || true
                '''
            }
        }

        stage('Create Docker Network') {
            steps {
                sh 'docker network create $NETWORK_NAME || true'
            }
        }

        stage('Run PostgreSQL') {
            steps {
                sh '''
                docker run -d --name $DB_CONTAINER \
                    --network $NETWORK_NAME \
                    -e POSTGRES_USER=myuser \
                    -e POSTGRES_PASSWORD=mypass \
                    -e POSTGRES_DB=mydb \
                    -p 5432:5432 \
                    $DB_IMAGE
                '''
            }
        }

        stage('Run Backend') {
            steps {
                sh '''
                docker run -d --name $BACKEND_CONTAINER \
                    --network $NETWORK_NAME \
                    -e DATABASE_URL=postgresql://myuser:mypass@$DB_CONTAINER:5432/mydb \
                    -e MODE=production \
                    -e PORT=3000 \
                    -p 3000:3000 \
                    $BACKEND_IMAGE
                '''
            }
        }

        stage('Run Frontend') {
            steps {
                sh '''
                docker run -d --name $FRONTEND_CONTAINER \
                    --network $NETWORK_NAME \
                    -p 80:80 \
                    $FRONTEND_IMAGE
                '''
            }
        }
    }

    post {
        always {
            echo "🚀 LMS deployment complete! Visit: http://<your-server-ip>"
        }
    }
}

