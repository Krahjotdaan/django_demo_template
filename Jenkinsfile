pipeline {
    agent {
        docker {image 'python:3.10'}
    }
    stages {
        stage('Task2') {
            steps {
                sh '''
                    python3 -m venv .venv 
                    . .venv/bin/activate
                    pip install -r requirements.txt
                    python manage.py test
                '''
            }
        }
    }
}