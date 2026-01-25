pipeline {
    agent any

    environment {
        AWS_REGION = 'eu-north-1'
        ECR_REPO = '135234114190.dkr.ecr.eu-north-1.amazonaws.com/python-devsecops-app'
        IMAGE_NAME = 'python-devsecops-app'
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Checkout from GitHub using PAT credential
                git branch: 'main',
                    url: 'https://github.com/Ayusheeuke/cloud-native-python-CI-CD-.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Dependency Scan - pip-audit') {
            steps {
                sh '''
                # Install pip-audit if not present
                if ! command -v pip-audit >/dev/null 2>&1; then
                    pip3 install --user pip-audit
                fi
                pip-audit -r requirements.txt
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Container Scan - Trivy') {
            steps {
                sh '''
                # Download Trivy standalone binary if not present
                if ! command -v trivy >/dev/null 2>&1; then
                    echo "Installing Trivy..."
                    curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh
                    chmod +x /usr/local/bin/trivy
                fi
                trivy image --severity HIGH,CRITICAL ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --pa_
