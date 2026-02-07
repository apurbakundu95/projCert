pipeline {
    agent any

    stages {

        stage('Install Puppet Agent') {
            steps {
                sh '''
                ssh test-server '
                sudo apt install puppet-agent -y
                '
                '''
            }
        }

        stage('Install Docker using Ansible') {
            steps {
                sh 'ansible-playbook ansible/docker-install.yml'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t php-app .
                docker run -d -p 8080:80 php-app
                '''
            }
        }

        stage('Deploy to Production') {
            steps {
                sh '''
                docker save php-app | ssh prod-server docker load
                ssh prod-server docker run -d -p 80:80 php-app
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

