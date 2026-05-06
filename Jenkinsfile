pipeline {
    agent any

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/todo-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip3 install -r requirements.txt'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                    # Kill any existing running instance
                    pkill -f "python3 app.py" || true

                    # Start the app in background
                    nohup python3 app.py > app.log 2>&1 &

                    echo "App deployed and running on port 5000"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed. Check logs.'
        }
    }
}