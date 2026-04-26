pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/piyushchhagani/jenkins-ec2-cicd-pipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'echo Build successful'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: '**/*', fingerprint: true
            }
        }
    }
}