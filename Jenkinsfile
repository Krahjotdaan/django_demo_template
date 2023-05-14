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
                    docker build . -t krahjotdaan/django_demo:${GIT_COMMIT} -t krahjotdaan/django_demo:latest
                '''
            }
        }
    }
}