pipeline {
    agent any

    environment {
        IMAGE = 'contactapp'
        DB_PASSWORD = credentials('db-password')
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build Image') {
            steps { sh 'docker build -t ${IMAGE}:${BUILD_NUMBER} .' }
        }
        stage('Test') {
            steps { sh 'docker run --rm ${IMAGE}:${BUILD_NUMBER} npm test' }
        }
        stage('Deploy') {
            steps {
                sh 'TAG=${BUILD_NUMBER} APP_PORT=80 docker compose -p contactapp-prod up -d'
            }
        }
        stage('Verify') {
            steps {
                sh '''
                    for i in 1 2 3 4 5 6; do
                        if curl -fs http://localhost:80 > /dev/null; then
                            echo "App is up"; exit 0
                        fi
                        echo "Waiting ($i/6)"; sleep 10
                    done
                    exit 1
                '''
            }
        }
    }
}
