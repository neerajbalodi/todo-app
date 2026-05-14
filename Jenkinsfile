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
         stage('App Deployed') {
            steps {
                sh '''
                  . venv/bin/activate
                  python3 app.py
                '''
            }
        }
    }
}
