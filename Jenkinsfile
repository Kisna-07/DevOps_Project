pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        DOCKER_IMAGE_NAME = 'krushna07/project-demo'
        TOMCAT_SERVER = 'http://20.62.44.175:8088/'
        TOMCAT_CREDENTIALS = credentials('tomcat-credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                // Check out the source code from Git
                checkout scm
            }
        }
        
        stage('Build and Test') {
            steps {
                // Build and test your Java Maven project
                sh 'mvn clean package'
            }
        }
        
        stage('Docker Build and Push') {
            steps {
                // Build a Docker image
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}")
                }
                
                // Authenticate and push the Docker image to Docker Hub
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    script {
                        docker.withRegistry("https://registry.hub.docker.com", 'docker-hub-credentials') {
                            dockerImage.push("${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}")
                        }
                    }
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                // Deploy the Docker image to Tomcat server
                withCredentials([usernamePassword(credentialsId: 'tomcat-credentials', usernameVariable: 'TOMCAT_USERNAME', passwordVariable: 'TOMCAT_PASSWORD')]) {
                    sh """
                    curl -T target/*.war http://${TOMCAT_USERNAME}:${TOMCAT_PASSWORD}@${TOMCAT_SERVER}/manager/text/deploy?path=/your-app-context&update=true
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo 'Build, Docker image push, and Tomcat deployment succeeded!'
        }
    }
}
