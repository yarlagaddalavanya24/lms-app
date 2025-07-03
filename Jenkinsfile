pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = 'lavanyayarlagadda24/webapp'
        BACKEND_IMAGE = 'lavanyayarlagadda24/api'
        FRONTEND_CONTAINER = 'frontend'
        BACKEND_CONTAINER = 'backend'
    }

    stages {
        stage('Cleanup Old Containers') {
            steps {
                sh """
                docker rm -f $FRONTEND_CONTAINER || true
                docker rm -f $BACKEND_CONTAINER || true
                """
            }
        }

        stage('Run Backend') {
            steps {
                sh """
                docker run -d --name $BACKEND_CONTAINER \\
                    -p 3000:3000 \\
                    -e PORT=3000 \\
                    $BACKEND_IMAGE
                """
            }
        }

        stage('Run Frontend') {
            steps {
                sh """
                docker run -d --name $FRONTEND_CONTAINER \\
                    -p 80:80 \\
                    $FRONTEND_IMAGE
                """
            }
        }
    }

    post {
        always {
            echo "🚀 LMS Frontend & Backend deployed (no DB). Visit: http://<your-server-ip>"
        }
    }
}

