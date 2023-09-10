pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
        DOCKER_IMAGE_NAME = 'krushna07/project-demo:v1'
        // TOMCAT_SERVER = 'http://20.62.44.175:8088/'
        // TOMCAT_CREDENTIALS = credentials('tomcat-credentials')
        // CUSTOM_DOCKERFILE_PATH = 'Dockerfile'  // Change this path
    }
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
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
                // withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'krushna07', passwordVariable: 'dckr_pat_N7DI_o068iwA9pYSiLaR0MVpsuc')]) {
                    script {
                        docker.withRegistry("https://registry.hub.docker.com", DOCKER_HUB_CREDENTIALS) {
                            dockerImage.push("${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}")
                        }
                    }
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                // Deploy the Docker image to Tomcat server
                // withCredentials([usernamePassword(credentialsId: 'tomcat-credentials', usernameVariable: 'TOMCAT_USERNAME', passwordVariable: 'TOMCAT_PASSWORD')]) {
                //     sh """
                //     curl -T target/your-web-app.war http://${TOMCAT_USERNAME}:${TOMCAT_PASSWORD}@${TOMCAT_SERVER}/manager/text/deploy?path=/your-app-context&update=true
                //     """
                // }
                echo "deploy stage"
                deploy adapters: [tomcat9 (
                    credentialsId: 'tomcat-credentials',
                    path: '',
                    url: 'http://20.62.44.175:8088/'
                )],
                contextPath: 'Planview',
                onFailure: 'false',
                war: 'target/*.war'
            }

            }
        }
    
    post {
        success {
            echo 'Build, custom Docker image push, and Tomcat deployment succeeded!'
        }
    }
