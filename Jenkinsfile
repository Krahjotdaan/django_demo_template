pipeline {
    agent none
    stages {
        stage('tests') {
        agent {
            docker {image 'python:3.10'}
        }
            steps {
                sh '''
                    python3 -m venv .venv 
                    . .venv/bin/activate
                    pip install -r requirements.txt
                    python manage.py test
                '''
            }
        }
        stage('build') {
            agent any
            steps {
                sh '''
                    sudo docker build -t Krahjotdaan/django_demo:${GIT_COMMIT}
                '''
            }
        }
    }
}
