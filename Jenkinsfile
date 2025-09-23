pipeline {
  agent any

  environment {
    REPO_URL = "https://github.com/ParimalaPeddakotla/c-code.git"
    DEV_HOST  = "ubuntu@13.232.48.230"
    UAT_HOST  = "ubuntu@13.126.143.37"
    PROD_HOST = "ubuntu@13.127.178.196"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'echo "Build stage can be used if you compile inside Jenkins"'
      }
    }

    stage('Deploy to Dev') {
      when { branch 'dev' }
      steps {
        sshagent(['ec2-key']) {
          sh """
            ssh -o StrictHostKeyChecking=no ${DEV_HOST} '
              set -e
              mkdir -p ~/app
              cd ~/app
              git pull origin dev || git clone ${REPO_URL} .
              gcc hello.c -o hello
              ./hello
            '
          """
        }
      }
    }

    stage('Deploy to UAT') {
      when { branch 'uat' }
      steps {
        sshagent(['ec2-key']) {
          sh """
            ssh -o StrictHostKeyChecking=no ${UAT_HOST} '
              set -e
              mkdir -p ~/app
              cd ~/app
              git pull origin uat || git clone ${REPO_URL} .
              gcc hello.c -o hello
              ./hello
            '
          """
        }
      }
    }

    stage('Deploy to Prod') {
      when { branch 'prod' }
      steps {
        sshagent(['ec2-key']) {
          sh """
            ssh -o StrictHostKeyChecking=no ${PROD_HOST} '
              set -e
              mkdir -p ~/app
              cd ~/app
              git pull origin prod || git clone ${REPO_URL} .
              gcc hello.c -o hello
              ./hello
            '
          """
        }
      }
    }
  }

  post {
    success { echo "Pipeline succeeded for branch ${env.BRANCH_NAME}" }
    failure { echo "Pipeline failed for branch ${env.BRANCH_NAME}" }
  }
}
