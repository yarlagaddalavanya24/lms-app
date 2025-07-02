pipeline {
    agent any

    environment {
        // Nexus credentials - adjust if your Jenkins has credentials IDs
        NEXUS_USERNAME = 'admin'
        NEXUS_PASSWORD = 'lms12345'
        NEXUS_URL      = 'http://35.90.115.123:8081/repository/lms'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build LMS') {
            steps {
                echo 'LMS Build Started'
                sh '''
                    cd webapp
                    npm install
                    npm run build
                '''
                echo 'LMS Build Completed'
            }
        }

        stage('Publish LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "LMS Version: ${packageJSONVersion}"

                    // Run ZIP in Docker so we don't depend on zip being installed on Jenkins node
                    sh """
                        docker run --rm \
                            -v \$PWD:/workspace \
                            -w /workspace \
                            alpine \
                            sh -c "apk add --no-cache zip && zip -r webapp/lms-${packageJSONVersion}.zip webapp/dist"
                    """

                    // Upload to Nexus
                    sh """
                        curl -v -u ${NEXUS_USERNAME}:${NEXUS_PASSWORD} \
                          --upload-file webapp/lms-${packageJSONVersion}.zip \
                          ${NEXUS_URL}/
                    """
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version

                    // Download artifact
                    sh """
                        curl -u ${NEXUS_USERNAME}:${NEXUS_PASSWORD} -X GET \
                          '${NEXUS_URL}/lms-${packageJSONVersion}.zip' \
                          --output lms-${packageJSONVersion}.zip
                    """

                    // Deploy to web server directory
                    sh """
                        sudo rm -rf /var/www/html/*
                        sudo unzip -o lms-${packageJSONVersion}.zip
                        sudo cp -r webapp/dist/* /var/www/html
                    """
                }
            }
        }

        stage('Clean Up Workspace') {
            steps {
                echo 'Cleaning workspace...'
                cleanWs()
            }
        }
    }
}

