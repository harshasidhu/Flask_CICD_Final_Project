pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds')
        IMAGE_NAME = 'harshavardhanjvv/flask-cicd-project:latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/harshasidhu/Flask_CICD_Final_Project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // ✅ Build using the Dockerfile inside app/
                    sh 'docker build -t $IMAGE_NAME -f app/Dockerfile app'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy to EC2 with Ansible') {
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
}
