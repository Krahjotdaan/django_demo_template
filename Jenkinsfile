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
            agent any
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_creds', 
                usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u ${USERNAME} -p ${PASSWORD}'
                    sh 'docker push krahjotdaan/django_demo_template:${GIT_COMMIT}'
                }
            }
        }
        stage("deploy") {
            agent any
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'deploy_server', keyFileVariable: 'KEY_FILE')]) {
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@http://devops.io12.me:8089/ mkdir -p ~${WORKSPACE}'
                    sh 'scp -o StrictHostKeyChecking=no -i ${KEY_FILE} docker-compose.yaml ${USERNAME}http://devops.io12.me:8089/:~${WORKSPACE}'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@http://devops.io12.me:8089/docker-compose -f ~${WORKSPACE}/docker-compose.yaml pull'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@http://devops.io12.me:8089/ docker-compose -f ~${WORKSPACE}/docker-compose.yaml up -d'
                }
            }
        }
    }
}