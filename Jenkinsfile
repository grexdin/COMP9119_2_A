pipeline {
    agent any

    environment {
        DOCKER_CREDS = credentials('docker-hub-credentials')
	IMAGE_TAG = "v${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build Docker Images') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin'
                sh 'docker compose push'
                sh 'docker logout'
            }
        }
        
        stage('Start Application') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }
}
