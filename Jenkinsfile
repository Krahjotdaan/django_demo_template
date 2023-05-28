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
                    sh 'docker push krahjotdaan/django_demo_template:latest'
                }
            }
        }
        stage("deploy") {
            agent any
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'deploy_server', keyFileVariable: 'KEY_FILE', usernameVariable: 'USERNAME')]) {
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me mkdir -p ~${WORKSPACE}'
                    sh 'scp -o StrictHostKeyChecking=no -i ${KEY_FILE} docker-compose.yaml ${USERNAME}@devops.io12.me:~${WORKSPACE}'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me docker-compose -f ~${WORKSPACE}/docker-compose.yaml pull'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me docker-compose -f ~${WORKSPACE}/docker-compose.yaml up -d'
                    sh 'scp -o StrictHostKeyChecking=no -i ${KEY_FILE} ostapenko.devops.io12.me.conf ${USERNAME}@devops.io12.me:~/nginx/'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me sudo certbot --nginx --non-interactive --agree-tos -m abc@yandex.ru -d ostapenko.devops.io12.me -d www.ostapenko.devops.io12.me'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me sudo systemctl reload nginx'
                }
            }
        }
    }
}