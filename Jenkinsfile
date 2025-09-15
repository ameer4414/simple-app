pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Use the full path for the 'docker' command to avoid 'command not found' errors.
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
                    def ecr_repo_url = '379632383729.dkr.ecr.ap-south-1.amazonaws.com'
                    def ecr_repo_name = 'jenkins-demo'
                    def full_image_tag = "${ecr_repo_url}/${ecr_repo_name}:simple-app-${env.BUILD_ID}"

                    // Use withCredentials to securely access AWS credentials
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'Jenkins-demo-aws-credentials']]) {
                        // Log in to ECR using the full paths for both aws and docker
                        sh "/usr/local/bin/aws ecr get-login-password --region ${aws_region} | /usr/local/bin/docker login --username AWS --password-stdin ${ecr_repo_url}"
                    
                        // Tag the image with the correct ECR repository name
                        sh "/usr/local/bin/docker tag simple-app:latest ${full_image_tag}"

                        // Push the image to ECR
                        sh "/usr/local/bin/docker push ${full_image_tag}"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Update the Kubernetes manifest file
                    sh "sed -i '' 's|DOCKER_IMAGE_TAG|simple-app:${env.BUILD_ID}|g' k8s-deployment.yml"

                    // Apply the updated manifest to the Kubernetes cluster
                    sh '/opt/homebrew/bin/kubectl apply -f k8s-deployment.yml'
                }
            }
        }
    }
}
