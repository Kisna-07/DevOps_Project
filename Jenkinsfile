pipeline {
    agent any

    environment {
        // Define environment variables
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_IMAGE_NAME = 'krushna07/project-demo:v1'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code from your version control system
                checkout scm
            }
        }

        stage('Build and Package') {
            steps {
                // Build your Java Maven application
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using your Dockerfile
                    def dockerImage = docker.build(DOCKER_IMAGE_NAME, "--file Dockerfile .")

                    // Push the Docker image to DockerHub
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS) {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Docker Image Build Successful!'
        }
        failure {
            echo 'Docker Image Build Failed!'
        }
    }
}
