pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        APP_EC2 = 'ubuntu@172.31.5.188'
        APP_DIR = '/home/ubuntu/todo-app'
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/neerajbalodi/todo-app.git'
            }
        }

        stage('Copy Code to App EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'app-ec2-ssh',
                    keyFileVariable: 'SSH_KEY'
                )]) {
                    sh '''
                        scp -i $SSH_KEY -o StrictHostKeyChecking=no -r * ${APP_EC2}:${APP_DIR}/
                    '''
                }
            }
        }

        stage('Create Virtual Env') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'app-ec2-ssh',
                    keyFileVariable: 'SSH_KEY'
                )]) {
                    sh '''
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no ${APP_EC2} "
                            cd ${APP_DIR}
                            python3 -m venv venv
                            . venv/bin/activate
                            pip install -r requirements.txt
                        "
                    '''
                }
            }
        }

        stage('Deploy App') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'app-ec2-ssh',
                    keyFileVariable: 'SSH_KEY'
                )]) {
                    sh '''
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no ${APP_EC2} "
                            pkill -f 'python3 app.py' || true
                            sleep 2
                            cd ${APP_DIR}
                            . venv/bin/activate
                            nohup python3 app.py > /tmp/app.log 2>&1 &
                            echo \$! > /tmp/app.pid
                            sleep 2
                            cat /tmp/app.log
                        "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ App deployed successfully on App EC2!'
        }
        failure {
            echo '❌ Deployment failed — check logs!'
        }
    }
}
