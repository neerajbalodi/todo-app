pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/neerajbalodi/todo-app.git'
            }
        }

        stage('Create Virtual Env') {
            steps {
                sh '''
                   python3 -m venv venv
                   . venv/bin/activate
                   pip install -r requirements.txt
                '''
            }
        }

        stage('Copy Files To EC2') {
            steps {
                sh '''
                scp -i /var/lib/jenkins/.ssh/app-ec2-key \
                -o StrictHostKeyChecking=no \
                -r Jenkinsfile app.log app.py requirements.txt templates venv \
                ubuntu@172.31.5.188:/home/ubuntu/todo-app/
                '''
            }
        }

        stage('Run App On EC2') {
            steps {
                sh '''
                ssh -i /var/lib/jenkins/.ssh/app-ec2-key \
                -o StrictHostKeyChecking=no \
                ubuntu@172.31.5.188 '
                    cd /home/ubuntu/todo-app
                    source venv/bin/activate
                    nohup python3 app.py > output.log 2>&1 &
                '
                '''
            }
        }
    }
}
