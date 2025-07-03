pipeline {
  agent any

  environment {
    BACKEND_IMAGE      = 'lavanyayarlagadda24/api:latest'
    FRONTEND_IMAGE     = 'lavanyayarlagadda24/webapp:latest'
    POSTGRES_IMAGE     = 'postgres:15'
    NETWORK_NAME       = 'lms-net'
    POSTGRES_CONTAINER = 'postgres_container'
    BACKEND_CONTAINER  = 'backend'
    FRONTEND_CONTAINER = 'frontend'
  }

  stages {
    stage('Pull Images') {
      steps {
        sh "docker pull $BACKEND_IMAGE"
        sh "docker pull $FRONTEND_IMAGE"
        sh "docker pull $POSTGRES_IMAGE"
      }
    }

    stage('Cleanup') {
      steps {
        sh '''
          docker rm -f $POSTGRES_CONTAINER $BACKEND_CONTAINER $FRONTEND_CONTAINER || true
          docker network rm $NETWORK_NAME          || true
        '''
      }
    }

    stage('Create Network') {
      steps {
        sh "docker network create $NETWORK_NAME || true"
      }
    }

    stage('Run PostgreSQL') {
      steps {
        sh """
          docker run -d --name $POSTGRES_CONTAINER \
            --network $NETWORK_NAME \
            -e POSTGRES_USER=myuser \
            -e POSTGRES_PASSWORD=mypass \
            -e POSTGRES_DB=mydb \
            -p 5432:5432 \
            $POSTGRES_IMAGE
        """
      }
    }

    stage('Run Backend') {
      steps {
        sh """
          docker run -d --name $BACKEND_CONTAINER \
            --network $NETWORK_NAME \
            -e DATABASE_URL=postgresql://myuser:mypass@$POSTGRES_CONTAINER:5432/mydb \
            -e PORT=3000 \
            -p 3000:3000 \
            $BACKEND_IMAGE
        """
      }
    }

    stage('Run Frontend') {
      steps {
        sh """
          docker run -d --name $FRONTEND_CONTAINER \
            --network $NETWORK_NAME \
            -p 80:80 \
            $FRONTEND_IMAGE
        """
      }
    }
  }

  post {
    always {
      echo "✅ LMS deployment pipeline finished!"
    }
  }
}

