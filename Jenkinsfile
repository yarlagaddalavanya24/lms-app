pipeline {
  agent any

  environment {
    BACKEND_IMAGE = 'lavanyayarlagadda24/api:latest'
    FRONTEND_IMAGE = 'lavanyayarlagadda24/webapp:latest'
  }

  stages {
    stage('Pull Docker Images') {
      steps {
        script {
          sh "docker pull $BACKEND_IMAGE"
          sh "docker pull $FRONTEND_IMAGE"
        }
      }
    }

    stage('Run Containers') {
      steps {
        script {
          // Create Docker network if not exists
          sh 'docker network create lms-net || true'

          // Run PostgreSQL container
          sh '''
            docker run -d --name postgres_container \
              --network lms-net \
              -e POSTGRES_USER=myuser \
              -e POSTGRES_PASSWORD=mypass \
              -e POSTGRES_DB=mydb \
              -p 5432:5432 postgres:15
          '''

          // Run Backend container
          sh '''
            docker run -d --name backend \
              --network lms-net \
              -e DATABASE_URL=postgresql://myuser:mypass@postgres_container:5432/mydb \
              -p 3000:3000 $BACKEND_IMAGE
          '''

          // Run Frontend container
          sh '''
            docker run -d --name frontend \
              --network lms-net \
              -p 80:80 $FRONTEND_IMAGE
          '''
        }
      }
    }
  }

  post {
    always {
      echo "Pipeline completed"
    }
  }
}

