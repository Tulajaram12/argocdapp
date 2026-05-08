pipeline {
    agent any

    environment {
        AWS_REGION = "eu-north-1"
        ECR_REPO = "188776114860.dkr.ecr.eu-north-1.amazonaws.com/namespace/appcode-ecr"
        IMAGE_TAG = "${BUILD_NUMBER}"   
        CLUSTER_NAME = "my-eks-cluster"

        APP_REPO = "https://github.com/Tulajaram12/appcode.git"
        HELM_REPO = "https://github.com/Tulajaram12/helm-charts.git"
    }

    stages {

        stage('Checkout App Code') {
            steps {
                dir('app') {
                    git branch: 'main', url: "${APP_REPO}"
                }
            }
        }

        stage('Checkout Helm Chart') {
            steps {
                dir('helm') {
                    git branch: 'main', url: "${HELM_REPO}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    sh 'docker build -t sample-app .'
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                docker tag sample-app:latest $ECR_REPO:$IMAGE_TAG
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to EKS using Helm') {
            steps {
                sh '''
                aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME

                helm upgrade --install sample-app ./helm/sample-app \
                  --set image.repository=$ECR_REPO \
                  --set image.tag=$IMAGE_TAG \
                  --force
                '''
            }
        }
    }
}
