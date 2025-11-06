pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIAL_ID = 'jenkins'          // Jenkins credential ID for Docker Hub login
        DOCKER_IMAGE_NAME = "rajshekharkg/hello-docker2"  // Docker Hub repo name
    }

    stages {
//        stage('Checkout Code') {
  //          steps {
    //            checkout scm                           // Pulls your Git repo code into Jenkins workspace
      //      }
      //  }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}")  // Build Docker image
                }
            }
        }
        stage('Run Unit Tests') {
            steps {
                script {
                    dockerImage.inside {
                        // Run unit tests using pytest inside the Docker container
                        sh 'pytest tests/unit --maxfail=1 --disable-warnings -q'
                    }
                }
            }
        }

        stage('Run Integration Tests') {
            steps {
                script {
                    dockerImage.inside {
                        // Run integration tests using pytest inside the container
                        sh 'pytest tests/integration --maxfail=1 --disable-warnings -q'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }  // Only push if build & tests passed
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIAL_ID) {
                        dockerImage.push()                          // Push the tagged image (with build number)
                        dockerImage.push('latest')                  // Push the 'latest' tag too
                    }
                }
            }
        }
    }
    post {
        success {
            echo "Build, test, and push completed successfully!"  // Runs after successful pipeline
        }
        failure {
            echo "Build failed."          // Runs after failure
        }
    }
}
