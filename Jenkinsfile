
pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }


 

    stages {
        stage ('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main',
                    url: 'https://github.com/Kisna-07/DevOps_Project.git'
            }
        }
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Install') {
            steps {
                sh "mvn install"
            }
        }
        stage ('Deploy') {
            steps {
                echo "Deploying"
                deploy adapters: [tomcat9 (
                    credentialsId: 'tom-admin',
                    path: '',
                    url: 'http://20.62.44.175:8088/'
                )],
                contextPath: 'Planview',
                onFailure: 'false',
                war: '**/*.war'
            }
            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.war'
                }
            }
        }



        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image from the Dockerfile
                    sh 'docker build -t krushna07/project-demo .'
                }
            }
        }

 

        stage('Publish to Docker Hub') {
            steps {
                script {

                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        // Push the Docker image to Docker Hub
                        docker.image('krushna07/project-demo').push()
                    }
                }
            }
        }
    }
}
