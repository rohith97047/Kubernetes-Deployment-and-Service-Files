pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('your-aws-access-key-id') // Replace 'your-aws-access-key-id'
        AWS_SECRET_ACCESS_KEY = credentials('your-aws-secret-access-key') // Replace 'your-aws-secret-access-key'
        ECR_REPOSITORY = 'your-ecr-repository-url' // Replace 'your-ecr-repository-url'
        IMAGE_TAG = "${env.BUILD_ID}"
        IMAGE_NAME = "${ECR_REPOSITORY}:${IMAGE_TAG}"
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
                    docker.withRegistry("${ECR_REPOSITORY}", AWS_ACCESS_KEY_ID) {
                        docker.push("${IMAGE_TAG}")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withKubeConfig(credentialsId: 'your-kubeconfig-credentials') { // Replace 'your-kubeconfig-credentials'
                        sh "kubectl apply -f kubernetes/deployment.yaml"
                        sh "kubectl apply -f kubernetes/service.yaml"
                    }
                }
            }
        }
    }
}
