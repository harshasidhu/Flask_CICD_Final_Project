pipeline {
  agent any

  environment {
    DOCKERHUB_REPO = "harshavardhanjvv/flask-cicd-project"
    IMAGE_TAG      = "latest"
    EC2_HOST       = "13.203.218.231"   // your app EC2 (nginx/web/db compose host)
  }

  options {
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps {
        checkout([$class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[
            url: 'https://github.com/harshasidhu/Flask_CICD_Final_Project.git',
            credentialsId: 'github-creds'
          ]]
        ])
      }
    }

    stage('Build Docker image') {
      steps {
        sh """
          docker build -t ${DOCKERHUB_REPO}:${IMAGE_TAG} -f app/Dockerfile app
        """
      }
    }

    stage('Docker Hub Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh """
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
          """
        }
      }
    }

    stage('Deploy to EC2 (SSH)') {
      steps {
        sshagent(credentials: ['ec2-ssh-key']) {
          sh """
            ssh -o StrictHostKeyChecking=no ubuntu@${EC2_HOST} '
              docker login -u ${env.DH_USER:-dummy} -p ${env.DH_PASS:-dummy} || true
              docker pull ${DOCKERHUB_REPO}:${IMAGE_TAG}

              # go to project dir and (re)start compose
              cd ~/Flask_CICD_Final_Project || git clone https://github.com/harshasidhu/Flask_CICD_Final_Project.git && cd Flask_CICD_Final_Project

              # use existing docker-compose.yml (already working on your EC2)
              docker compose down || true
              docker compose up -d --pull always --force-recreate
            '
          """
        }
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}
