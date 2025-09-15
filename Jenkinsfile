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
            withEnv([
                "PATH+AWS_CLI=/usr/local/bin",
                "PATH+DOCKER_BIN=/usr/local/bin"
            ]) {
                // Log in to ECR using the AWS CLI
                sh '/usr/local/bin/aws ecr get-login-password --region ap-south-1 | /usr/local/bin/docker login --username AWS --password-stdin 379632383729.dkr.ecr.ap-south-1.amazonaws.com'

                // Tag the image
                sh '/usr/local/bin/docker tag simple-app:latest 379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo/simple-app:latest'
                
                // Push the tagged image to ECR
                sh '/usr/local/bin/docker push 379632383729.dkr.ecr.ap-south-1.amazonaws.com/jenkins-demo/simple-app:latest'
            }
        }
    }
}
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