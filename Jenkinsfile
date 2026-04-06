pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        AWS_ACCOUNT_ID = "880192676145"
        ECR_REPO = "ecs-repo"
        IMAGE_NAME = "ecs-repo"
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/poorna484/ecs-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'aws-cred',   // ✅ UPDATED HERE
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_URI
                    '''
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag $IMAGE_NAME:latest $ECR_URI:latest'
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh 'docker push $ECR_URI:latest'
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed to ECR successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
