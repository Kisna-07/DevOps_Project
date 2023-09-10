pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_IMAGE_NAME = 'krushna07/project-demo:v1'
        TOMCAT_SERVER = 'http://20.62.44.175:8088'
        TOMCAT_CREDENTIALS = credentials('tom-admin')
        CUSTOM_DOCKERFILE_PATH = '**/Dockerfile'  // Change this path
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
        
        stage('Build Custom Docker Image') {
            steps {
                // Build a custom Docker image based on Tomcat
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}", "-f ${CUSTOM_DOCKERFILE_PATH} .")
                }
                
                // Authenticate and push the Docker image to Docker Hub
                withCredentials([usernamePassword(credentialsId: 'dockerhub')]) {
                    script {
                        docker.withRegistry("https://registry.hub.docker.com", 'dockerhub') {
                            dockerImage.push("${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}")
                        }
                    }
                }
            }
        }
        
        // stage('Deploy to Tomcat') {
        //     steps {
        //         // Deploy the Docker image to Tomcat server
        //         withCredentials([usernamePassword(credentialsId: 'tom-admin')]) {
        //             sh """
        //             curl -T target/your-web-app.war http://${TOMCAT_USERNAME}:${TOMCAT_PASSWORD}@${TOMCAT_SERVER}/manager/text/deploy?path=/your-app-context&update=true
        //             """
        //         }
        //     }
        // }
    }
    
    post {
        success {
            echo 'Build, custom Docker image push, and Tomcat deployment succeeded!'
        }
    }
}
