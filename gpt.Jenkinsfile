pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate

                pip install --upgrade pip
                pip install poetry

                poetry install
                '''
            }
        }

        stage('Quality') {
            steps {
                sh '''
                . venv/bin/activate

                poetry run black --check .
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                . venv/bin/activate

                poetry run python -m py_compile *.py
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploy stage placeholder'
            }
        }
    }
}