pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "lingeshv09092005/jenkinswebapp"   // Your Docker Hub repo
        CONTAINER_NAME = "jenkinswebapp-container"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/lingeshv520/jenkinswebapp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Force fresh build with no cache
                    sh "docker build --no-cache -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Remove old container, pull latest image, and redeploy
                    sh """
                    docker rm -f ${CONTAINER_NAME} || true
                    docker pull ${DOCKER_IMAGE}:latest
                    docker run -d --name ${CONTAINER_NAME} -p 80:80 ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }

    triggers {
        pollSCM('* * * * *')   // Check every 1 minute for changes
    }
}
