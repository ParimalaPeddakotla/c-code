pipeline {
    agent any
    environment {
        DEV_HOST = "ubuntu@43.204.149.91"
        UAT_HOST = "ubuntu@65.1.106.180"
        PROD_HOST = "ubuntu@3.110.124.171"
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: "master", url: 'https://github.com/ParimalaPeddakotla/c-code.git'
            }
        }

        stage('Build Locally (Jenkins server)') {
            steps {
                sh 'gcc hello.c -o hello'
            }
        }

        stage('Test Locally (Jenkins server)') {
            steps {
                sh './hello'
            }
        }

        stage('Deploy to Dev') {
            when { branch 'dev' }
            steps {
                sshagent(['github-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $DEV_HOST '
                        mkdir -p ~/app &&
                        cd ~/app &&
                        git pull origin dev || git clone https://github.com/ParimalaPeddakotla/c-code.git . &&
                        gcc hello.c -o hello &&
                        ./hello
                        '
                    """
                }
            }
        }

        stage('Deploy to UAT') {
            when { branch 'uat' }
            steps {
                sshagent(['github-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $UAT_HOST '
                        mkdir -p ~/app &&
                        cd ~/app &&
                        git pull origin uat || git clone https://github.com/ParimalaPeddakotla/c-code.git . &&
                        gcc hello.c -o hello &&
                        ./hello
                        '
                    """
                }
            }
        }

        stage('Deploy to Prod') {
            when { branch 'master' }
            steps {
                sshagent(['github-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $PROD_HOST '
                        mkdir -p ~/app &&
                        cd ~/app &&
                        git pull origin master || git clone https://github.com/ParimalaPeddakotla/c-code.git . &&
                        gcc hello.c -o hello &&
                        ./hello
                        '
                    """
                }
            }
        }
    }
}
