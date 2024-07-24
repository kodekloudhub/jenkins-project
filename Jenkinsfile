pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/kodekloudhub/jenkins-project.git'
                sh "ls -ltr"
            }
        }
        stage('Setup') {
            steps {
                sh "pip install -r requirements.txt"
            }
        }
        stage('Test') {
            steps {
                sh "pytest"
            }
        }
    }
}