pipeline {
    agent any

    environment {
        EC2_USER = "ec2-user"
        EC2_HOST = "13.126.21.129"
        KEY_PATH = "/var/lib/jenkins/jenkins-ec2-key.pem"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/piyushchhagani/jenkins-ec2-cicd-pipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'echo Build successful'
                sh 'tar -cvf app.tar app.js package.json'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'app.tar', fingerprint: true
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                scp -o StrictHostKeyChecking=no -i $KEY_PATH app.tar $EC2_USER@$EC2_HOST:/home/ec2-user/
                '''
            }
        }

        stage('Run App on EC2') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $EC2_USER@$EC2_HOST "
                tar -xvf app.tar
                node app.js &
                "
                '''
            }
        }
    }
}