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
                    -r app.py requirements.txt templates \
                    ubuntu@172.31.2.214:/home/ubuntu/todo-app/
                '''
            }
        }

        stage('Run App On EC2') {
            steps {
                sh '''
                    ssh -i /var/lib/jenkins/.ssh/app-ec2-key \
                    -o StrictHostKeyChecking=no \
                    ubuntu@172.31.2.214 bash << 'ENDSSH'
                        pkill -f "python3 app.py" || true
                        sleep 2
                        cd /home/ubuntu/todo-app
                        python3 -m venv venv
                        source venv/bin/activate
                        pip install -r requirements.txt
                        nohup python3 app.py > /tmp/app.log 2>&1 &
                        disown
                        sleep 2
                        cat /tmp/app.log
ENDSSH
                '''
            }
        }
    }

    post {
        success {
            echo '✅ App deployed successfully on App EC2!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
