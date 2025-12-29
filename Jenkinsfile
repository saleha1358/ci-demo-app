pipeline {
  agent any

  environment {
    AWS_REGION = "ap-south-1"
    ECR_REPO = "135201266881.dkr.ecr.ap-south-1.amazonaws.com/ci-demo-app"
    CLUSTER_NAME = "ci-demo-cluster"
    SERVICE_NAME = "ci-demo-service"
  }

  stages {

    stage('Build & Test') {
      steps {
        sh 'python3 -m pip install -r requirements.txt'
        sh 'python3 -m pytest'
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t ci-demo-app:latest .'
      }
    }

    stage('Login to ECR') {
      steps {
        sh '''
        aws ecr get-login-password --region $AWS_REGION \
        | docker login --username AWS --password-stdin $ECR_REPO
        '''
      }
    }

    stage('Push Image to ECR') {
      steps {
        sh '''
        docker tag ci-demo-app:latest $ECR_REPO:latest
        docker push $ECR_REPO:latest
        '''
      }
    }

    stage('Deploy to ECS') {
      steps {
        sh '''
        aws ecs update-service \
          --cluster $CLUSTER_NAME \
          --service $SERVICE_NAME \
          --region $AWS_REGION \
          --force-new-deployment
        '''
      }
    }
  }
}

