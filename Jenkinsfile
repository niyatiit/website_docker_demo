pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = 'website-docker-demo'
        AWS_ACCOUNT_ID = '135775632220'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}"
        LATEST_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
        DEPLOY_SERVER = '15.207.85.126'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/niyatiit/website_docker_demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t website-docker-demo .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                bat '''
                    docker tag website-docker-demo:latest %IMAGE_URI%
                    docker tag website-docker-demo:latest %LATEST_URI%
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                bat '''
                    aws ecr get-login-password --region %AWS_REGION% > password.txt
                    type password.txt | docker login --username AWS --password-stdin %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                bat '''
                    docker push %IMAGE_URI%
                    docker push %LATEST_URI%
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploy stage skipped on Windows Jenkins. Use Linux agent for full deploy.'
            }
        }
    }

    post {
        success {
            echo 'Build and push completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}