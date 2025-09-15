pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh "/usr/local/bin/docker build --platform linux/amd64 -t simple-app:latest ."                
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
                    def ecr_repo_url = '379632383729.dkr.ecr.ap-south-1.amazonaws.com'
                    def ecr_repo_name = 'jenkins-demo'
                    def full_image_tag = "${ecr_repo_url}/${ecr_repo_name}:simple-app-${env.BUILD_ID}"

                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'Jenkins-demo-aws-credentials']]) {
                        sh "/usr/local/bin/aws ecr get-login-password --region ${aws_region} | /usr/local/bin/docker login --username AWS --password-stdin ${ecr_repo_url}"
                        
                        sh "/usr/local/bin/docker tag simple-app:latest ${full_image_tag}"

                        sh "/usr/local/bin/docker push ${full_image_tag}"
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Define the full image tag variable, including the ECR URL
                    def ecr_repo_url = '379632383729.dkr.ecr.ap-south-1.amazonaws.com'
                    def ecr_repo_name = 'jenkins-demo'
                    def full_image_tag_for_k8s = "${ecr_repo_url}/${ecr_repo_name}:simple-app-${env.BUILD_ID}"
                    
                    // This block ensures both aws and kubectl are found
                    withEnv(['PATH+EXTRA=/usr/local/bin:/opt/homebrew/bin']) {
                        // Update the Kubernetes manifest file with the full ECR URL
                        sh "sed -i '' 's|DOCKER_IMAGE_TAG|${full_image_tag_for_k8s}|g' k8s-deployment.yml"

                        // Apply the updated manifest to the Kubernetes cluster
                        sh 'kubectl apply -f k8s-deployment.yml'
                    }
                }
            }
        }
    }
}
