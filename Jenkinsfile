pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('your-aws-access-key-id') // Replace 'your-aws-access-key-id'
        AWS_SECRET_ACCESS_KEY = credentials('your-aws-secret-access-key') // Replace 'your-aws-secret-access-key'
        ECR_REPOSITORY = 'your-ecr-repository-url' // Replace 'your-ecr-repository-url'
        IMAGE_TAG = "${env.BUILD_ID}"
        IMAGE_NAME = "${ECR_REPOSITORY}:${IMAGE_TAG}"
        AWS_REGION = "us-east-1" // replace with your AWS region.
        CLUSTER_NAME = "simple-cluster" // replace with your cluster name.
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rohith97047/Kubernetes-Deployment-and-Service-Files.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}", '.')
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'your-aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'), string(credentialsId: 'your-aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REPOSITORY.split('/')[0]}"
                        docker.push("${IMAGE_TAG}")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'your-aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'), string(credentialsId: 'your-aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh "aws eks --region ${AWS_REGION} update-kubeconfig --name ${CLUSTER_NAME}"
                        sh "kubectl apply -f kubernetes/deployment.yaml"
                        sh "kubectl apply -f kubernetes/service.yaml"
                    }

                }
            }
        }
    }
}
