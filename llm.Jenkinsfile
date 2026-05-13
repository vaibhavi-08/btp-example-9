pipeline {
    agent any

    environment {
        PYTHON = 'python3'
        PIP    = 'pip3'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                echo 'Installing dependencies via Poetry...'
                sh '''
                    pip3 install poetry --quiet
                    poetry install --no-interaction --no-ansi
                '''
            }
        }

        stage('Quality') {
            steps {
                echo 'Running Black formatter check...'
                sh '''
                    poetry run black --check .
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building package...'
                sh '''
                    poetry build
                '''
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline completed successfully.'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
        always {
            cleanWs()
        }
    }
}