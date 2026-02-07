pipeline {
    agent any

    stages {

        stage('Install Puppet Agent') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@18.212.190.111 \
                "sudo apt update && sudo apt install puppet-agent -y"
                '''
            }
        }

        stage('Install Docker using Ansible') {
            steps {
                sh 'ansible-playbook -i ansible/hosts ansible/docker-install.yaml'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t php-app .
                docker run -d -p 8090:80 php-app
                '''
            }
        }

        stage('Deploy to Production') {
            steps {
                sh '''
                docker save php-app | ssh prod-server docker load
                ssh prod-server docker run -d -p 8090:80 php-app
                '''
            }
        }
    }

    post {
        failure {
            sh '''
            ssh test-server docker rm -f php-app || true
            '''
        }
    }
}

