pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/harshasidhu/Flask_CICD_Final_Project.git'
        DOCKER_IMAGE = 'harshavardhanjvv/flask-cicd-project'
        DH_USER = credentials('docker-hub-creds')      // Your Docker Hub username ID in Jenkins credentials
        DH_PASS = credentials('docker-hub-creds')      // Your Docker Hub password/token
        EC2_SSH = 'ec2-ssh-key'                        // SSH Key ID (flask-key.pem added in Jenkins)
        EC2_HOST = 'ubuntu@13.203.218.231'             // Replace with actual EC2 public IP
    }

    stages {
        
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh """
                    echo ${DH_PASS} | docker login -u ${DH_USER} --password-stdin
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh """
                    docker push ${DOCKER_IMAGE}:latest
                """
            }
        }

        stage('Deploy on EC2') {
            steps {
                sshagent(credentials: ["${EC2_SSH}"]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_HOST} '
                        docker pull ${DOCKER_IMAGE}:latest &&
                        docker stop flask-app || true &&
                        docker rm flask-app || true &&
                        docker run -d -p 5000:5000 --name flask-app ${DOCKER_IMAGE}:latest
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Build or Deployment Failed!"
        }
    }
}
