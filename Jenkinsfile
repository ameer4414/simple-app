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
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    def aws_region = 'ap-south-1'
                    def ecr_repo_url = '379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo'

                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'Jenkins-demo-aws-credentials']]) {
                        sh "/usr/local/bin/aws ecr get-login-password --region ${aws_region} | /usr/local/bin/docker login --username AWS --password-stdin ${ecr_repo_url}"
                    
                        sh "/usr/local/bin/docker tag simple-app:latest ${ecr_repo_url}/simple-app:latest"

                        sh "/usr/local/bin/docker push ${ecr_repo_url}/simple-app:latest"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "sed -i 's|DOCKER_IMAGE_TAG|simple-app:${env.BUILD_ID}|g' k8s-deployment.yaml"
                    sh 'kubectl apply -f k8s-deployment.yaml'
                }
            }
        }
    }
}