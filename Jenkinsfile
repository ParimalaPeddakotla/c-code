pipeline {
    agent any

    environment {
        DEV_HOST = "ubuntu@43.204.149.91"
        UAT_HOST = "ubuntu@65.1.106.180"
        PROD_HOST = "ubuntu@3.110.124.171"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Locally') {
            steps {
                sh 'gcc hello.c -o hello'
            }
        }

        stage('Test Locally') {
            steps {
                sh './hello'
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'dev'
                    branch 'uat'
                    branch 'master'
                }
            }
            steps {
                script {
                    def host = ''
                    def sshKey = ''

                    if (env.BRANCH_NAME == 'dev') {
                        host = DEV_HOST
                        sshKey = 'ec2-key'
                    } else if (env.BRANCH_NAME == 'uat') {
                        host = UAT_HOST
                        sshKey = 'ec2-key'
                    } else if (env.BRANCH_NAME == 'master') {
                        host = PROD_HOST
                        sshKey = 'ec2-key'
                    }

                    sshagent([sshKey]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no $host '
                            mkdir -p ~/app &&
                            cd ~/app &&
                            git pull origin ${env.BRANCH_NAME} || git clone https://github.com/ParimalaPeddakotla/c-code.git . &&
                            gcc hello.c -o hello &&
                            ./hello
                            '
                        """
                    }
                }
            }
        }
    }
}
