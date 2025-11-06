pipeline {
        agent any
        environment {
            DOCKER_HUB_CREDENTIAL_ID = 'jenkins' // Replace with your Jenkins credential ID for Docker Hub
            DOCKER_IMAGE_NAME = "rajshekharkg/hello-docker2" // Replace with your Docker Hub username and image name
        }
        stages {
            stage('Build Docker Image') {
                steps {
                    script {
                        dockerImage = docker.build "${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                    }
                }
            }
            stage('Push Docker Image') {
                steps {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIAL_ID) {
                            dockerImage.push()
                        }
                    }
                }
            }
        }
    }