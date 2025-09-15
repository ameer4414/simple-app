pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh "/usr/local/bin/docker build -t simple-app:latest ."
                }
            }
        }
        stage('Test Application') {
            steps {
                script {
                    echo 'Running automated tests...'
                    // This is where you would call your test command, e.g.:
                    // sh 'npm test'
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    // Define ECR variables for readability and reusability
                    def aws_region = 'ap-south-1'
                    def ecr_repo_url = '379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo'
                    def image_tag = "${ecr_repo_url}:${env.BUILD_ID}"

                    // Log in to ECR with the credentials from the AWS CLI
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'Jenkins-demo-aws-credentials']]) {
                        sh "aws ecr get-login-password --region ${aws_region} | docker login --username AWS --password-stdin ${ecr_repo_url}"
                    }

                    // Tag the image
                    sh "docker tag simple-app:${env.BUILD_ID} ${image_tag}"

                    // Push the image to ECR
                    sh "docker push ${image_tag}"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Update the Kubernetes manifest file
                    sh "sed -i 's|DOCKER_IMAGE_TAG|simple-app:${env.BUILD_ID}|g' k8s-deployment.yaml"

                    // Apply the updated manifest to the Kubernetes cluster
                    sh 'kubectl apply -f k8s-deployment.yaml'
                }
            }
        }
    }
}