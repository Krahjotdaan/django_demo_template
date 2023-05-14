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
                    docker build . -t krahjotdaan/django_demo_template:${GIT_COMMIT} -t krahjotdaan/django_demo_template:latest
                '''
            }
        }
        stage('push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_creds', 
                usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u ${USERNAME} -p ${PASSWORD}'
                    sh 'docker push krahjotdaan/django_demo_template:${GIT_COMMIT}'
                }
            }
        }
    }
}