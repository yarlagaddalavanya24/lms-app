pipeline {
    agent any

    stages {

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
                    echo "App version: ${packageJSONVersion}"

                    // Use Docker to run zip
                    sh """
                        docker run --rm \
                          -v \$PWD:/workspace \
                          -w /workspace \
                          alpine \
                          sh -c "apk add --no-cache zip && zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                    """

                    sh """
                        curl -v -u admin:lms12345 \
                          --upload-file webapp/lms-${packageJSONVersion}.zip \
                          http://35.90.115.123:8081/repository/lms/
                    """
                }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "Deploying LMS version: ${packageJSONVersion}"

                    sh """
                        curl -u admin:lms12345 -X GET \
                          'http://35.90.115.123:8081/repository/lms/lms-${packageJSONVersion}.zip' \
                          --output lms-${packageJSONVersion}.zip
                    """

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
                echo 'Cleaning Work Space'
                cleanWs()
            }
        }
    }
}

