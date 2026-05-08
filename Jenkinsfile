pipeline {
    agent any

    environment {
        AWS_REGION = "eu-north-1"
        ECR_REPO = "188776114860.dkr.ecr.eu-north-1.amazonaws.com/namespace/appcode-ecr"
        IMAGE_TAG = "${BUILD_NUMBER}"

        APP_REPO = "https://github.com/Tulajaram12/argocdapp.git"
        HELM_REPO = "https://github.com/Tulajaram12/new-helm-charts.git"
    }

    stages {

        stage('Checkout App Code') {
            steps {
                dir('app') {
                    git branch: 'Main', url: "${APP_REPO}"
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
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin 188776114860.dkr.ecr.eu-north-1.amazonaws.com

                docker tag sample-app:latest $ECR_REPO:$IMAGE_TAG

                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Update Helm values.yaml') {
            steps {
                dir('helm/sample-app') {
                    sh """
                    sed -i 's|tag: .*|tag: "${IMAGE_TAG}"|g' values.yaml

                    echo "Updated values.yaml:"
                    cat values.yaml
                    """
                }
            }
        }

        stage('Commit and Push Helm Changes') {
            steps {
                dir('helm') {
                    sh """
                    git config user.email "tulajaramkamble@gmail.com"
                    git config user.name "tulajaram"

                    git add .

                    git commit -m "Update image tag to ${IMAGE_TAG}" || echo "No changes to commit"

                    git push -u origin main
                    """
                }
            }
        }
    }
}
