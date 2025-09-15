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
            // Define ECR variables for readability
            def aws_region = 'ap-south-1'
            def ecr_repo_url = '379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo'

            // Use withCredentials to securely access AWS credentials
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'Jenkins-demo-aws-credentials']]) {
                // Log in to ECR using the full paths for both aws and docker
                sh "/usr/local/bin/aws ecr get-login-password --region ${aws_region} | /usr/local/bin/docker login --username AWS --password-stdin ${ecr_repo_url}"
            
                // Tag the image
                sh "/usr/local/bin/docker tag simple-app:latest ${ecr_repo_url}/simple-app:latest"

                // Push the image to ECR
                sh "/usr/local/bin/docker push ${ecr_repo_url}/simple-app:latest"
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