pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t two-tier-flask-app .'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }
}
