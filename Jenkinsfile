pipeline {
  agent any

  environment {
    BACKEND_IMAGE = 'lavanyayarlagadda24/api:latest'
    FRONTEND_IMAGE = 'lavanyayarlagadda24/webapp:latest'
  }
    stage('Build Docker Images') {
      steps {
        script {
          sh 'docker build -t $BACKEND_IMAGE ./backend'
          sh 'docker build -t $FRONTEND_IMAGE ./frontend'
        }
      }
    }

    stage('Run Containers') {
      steps {
        script {
          sh 'docker network create lms-net || true'
          sh '''
            docker run -d --name postgres_container \
              --network lms-net \
              -e POSTGRES_USER=myuser \
              -e POSTGRES_PASSWORD=mypass \
              -e POSTGRES_DB=mydb \
              -p 5432:5432 postgres:15

            docker run -d --name backend \
              --network lms-net \
              -e DATABASE_URL=postgresql://myuser:mypass@postgres_container:5432/mydb \
              -p 3000:3000 $BACKEND_IMAGE

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


