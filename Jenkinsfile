pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO   = "193363646141.dkr.ecr.us-east-1.amazonaws.com/flask-app"
        CLUSTER    = "Flask-cluster"
        SERVICE    = "flask-service"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/DharaniOps/flask-ecs-project.git'
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin $ECR_REPO
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $ECR_REPO:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                aws ecs update-service \
                    --cluster $CLUSTER \
                    --service $SERVICE \
                    --force-new-deployment \
                    --region $AWS_REGION
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Deployment triggered successfully!"
                echo "Check ECS console → Service events for progress."
                echo "Then open ALB DNS to confirm app update."
            }
        }
    }
}
