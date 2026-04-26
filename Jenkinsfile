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
        sh '''
        echo "Building application..."

        # Clean old artifacts
        rm -f app.tar

        # Verify files exist
        ls -l app.js package.json

        # Show content BEFORE packaging
        echo "----- LOCAL package.json -----"
        cat package.json
        echo "------------------------------"

        # Create archive
        tar -cvf app.tar app.js package.json

        # Verify archive
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

                echo "Verifying file on EC2..."
                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $EC2_USER@$EC2_HOST "
                ls -lh app.tar
                "
                '''
            }
        }

        stage('Run App on EC2') {
            steps {
                sh '''
                echo "Running application on EC2..."

                ssh -o StrictHostKeyChecking=no -i $KEY_PATH $EC2_USER@$EC2_HOST "
                
                # Clean previous deployment
                rm -rf app
                mkdir app

                # Wait to ensure file is fully written
                sleep 2

                # Extract fresh files
                tar -xvf app.tar -C app

                cd app

                # Debug: show file content
                echo '----- package.json -----'
                cat package.json
                echo '------------------------'

                # Run application
                node app.js &

                echo 'App started successfully 🚀'
                "
                '''
            }
        }
    }
}