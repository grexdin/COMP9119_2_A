pipeline {
    agent any

    environment {
        DOCKER_CREDS = credentials('docker-hub-credentials')
        IMAGE_TAG = "v${env.BUILD_NUMBER}"
        IMAGE_NAME = "grexdin/fast-app-a" 
        CONTAINER_NAME = "my-fastapi-app"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                // Change into the fast-app directory before building
                dir('fast-app') {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin'
                // Push both the specific version tag and the latest tag
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${IMAGE_NAME}:latest"
                sh 'docker logout'
            }
        }
        
        stage('Start Application') {
            steps {
                // Stop and remove the old container if it exists so the port isn't blocked
                sh "docker rm -f ${CONTAINER_NAME} || true"
                
                // Run the new container, mapping host port 80 to container port 80
                sh "docker run -d --name ${CONTAINER_NAME} -p 80:80 ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }
}
