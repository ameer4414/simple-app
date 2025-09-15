pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t simple-app:latest .'
                }
            }
        }
        stage('Test Application') {
            steps {
                script {
                    // You would run automated tests here
                    // For example: sh 'npm test' or sh 'docker run simple-app:latest'
                    echo 'Running tests...'
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    // Log in to ECR
                    sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo'

                    // Tag the image with a unique ID and push
                    sh 'docker tag simple-app:latest 379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo/simple-app:latest'
                    sh 'docker push 379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo/simple-app:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use a tool like `kubectl` to deploy
                    // Update a manifest file with the new image tag and apply it to the cluster
                    sh 'kubectl apply -f k8s-deployment.yaml'
                }
            }
        }
    }
}