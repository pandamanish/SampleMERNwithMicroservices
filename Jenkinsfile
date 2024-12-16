pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '975050024946'
        AWS_REGION = 'us-west-2'

        // ECR repository URIs for each service
        FRONTEND_REPO_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/mern-frontend"
        HELLO_REPO_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/hello-service"
        PROFILE_REPO_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/profile-service"

        // Tag for the Docker images
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                script {
                    // Log in to ECR
                    sh """
                    aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Build Docker images for each service
                    sh """
                    docker build -t mern-frontend ./frontend
                    docker build -t hello-service ./helloService
                    docker build -t profile-service ./profileService
                    """
                }
            }
        }

        stage('Tag and Push Docker Images') {
            steps {
                script {
                    // Tag and push each image to its respective ECR repository
                    sh """
                    docker tag mern-frontend:latest ${FRONTEND_REPO_URI}:${IMAGE_TAG}
                    docker push ${FRONTEND_REPO_URI}:${IMAGE_TAG}

                    docker tag hello-service:latest ${HELLO_REPO_URI}:${IMAGE_TAG}
                    docker push ${HELLO_REPO_URI}:${IMAGE_TAG}

                    docker tag profile-service:latest ${PROFILE_REPO_URI}:${IMAGE_TAG}
                    docker push ${PROFILE_REPO_URI}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}
