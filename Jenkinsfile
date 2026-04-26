pipeline {
    agent any

    environment {
        EC2_USER = "ec2-user"
        EC2_HOST = "13.126.21.129"
        KEY_PATH = "/var/lib/jenkins/jenkins-ec2-key.pem"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/piyushchhagani/jenkins-ec2-cicd-pipeline.git'
            }
        }

        stage('Verify Files') {
            steps {
                sh '''
                echo "===== FILE CHECK ====="
                ls -l
                echo "===== app.js ====="
                cat app.js
                echo "===== package.json ====="
                cat package.json
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                echo "Building application..."

                rm -f app.tar

                tar -cvf app.tar app.js package.json

                echo "===== TAR CONTENT ====="
                tar -tvf app.tar
                '''
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
                echo "Copying artifact to EC2..."

                scp -o StrictHostKeyChecking=no -i $KEY_PATH app.tar $EC2_USER@$EC2_HOST:/home/ec2-user/

                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $EC2_USER@$EC2_HOST "
                echo 'File received:'
                ls -lh app.tar
                "
                '''
            }
        }

        stage('Run App on EC2') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $EC2_USER@$EC2_HOST "

                echo 'Stopping old app if running...'
                pkill node || true

                rm -rf app
                mkdir app

                tar -xvf app.tar -C app

                cd app

                echo '----- package.json -----'
                cat package.json
                echo '------------------------'

                echo 'Starting app...'
                nohup node app.js > app.log 2>&1 &

                echo 'App started successfully 🚀'
                "
                '''
            }
        }
    }
}