pipeline {
    agent none

    environment {
        DOCKER_IMAGE_NAME = 'ingyelkhateeb/devops-project-alx_swd1_m2d'
        DOCKER_IMAGE_TAG = 'latest' //
        DOCKER_RUN_PORT = '8082'
    }
    stages {
        stage('Checkout Code') {
            agent { label 'agent1' } // Run this stage on agent1
            steps {
                // Checkout your code from the repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            agent { label 'agent1' } // Build on agent1
            steps {
                script {
                    // Build the Docker image using the Dockerfile in the repo
                    docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            agent { label 'agent1' } // Push on agent1
            steps {
                script {
                    // Push the image to Docker Hub
                    docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
                }
            }
        }

        stage('Pull Docker Image on Agent 2') {
            agent { label 'agent2' } // Pull on agent2
            steps {
                script {
                    // Pull the image on agent2
                    sh "docker pull ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage('Run Docker Image on Agent 2') {
            agent { label 'agent2' } // Run on agent2
            steps {
                script {
                    // Run the Docker image, mapping the necessary ports
                    sh "docker run -d -p ${DOCKER_RUN_PORT}:${DOCKER_RUN_PORT} ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage('Check Connectivity on Agent 2') {
            agent { label 'agent2' } // Check on agent2
            steps {
                script {
                    // Check if the website is accessible using curl
                    def response = sh(script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:${DOCKER_RUN_PORT}", returnStdout: true).trim()

                    // Check if the response code is 200 (OK)
                    if (response == '200') {
                        echo 'Website is accessible. Pipeline succeeded.'
                    } else {
                        error "Website is not accessible. Response code: ${response}. Pipeline failed."
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
