pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds')
        IMAGE_NAME = 'harshavardhanjvv/flask-cicd-project:latest'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/harshasidhu/Flask_CICD_Final_Project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // ✅ Build image using Dockerfile inside app folder
                    sh 'docker build -t $IMAGE_NAME -f app/Dockerfile app'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy on EC2 using Ansible') {
            steps {
                script {
                    sh '''
                    cd ansible
                    ansible-playbook -i inventory.ini deploy.yml --extra-vars "docker_image=${IMAGE_NAME}"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and deployment successful!'
        }
        failure {
            echo '❌ Build failed. Please check logs.'
        }
    }
}
