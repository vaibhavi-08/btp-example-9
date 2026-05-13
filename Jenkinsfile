pipeline {
    agent any

    environment {
        REGISTRY_CREDS   = 'dockerhub-credentials'
        DEPLOY_SSH_CREDS = 'deploy-server-ssh'
        DOCKER_REGISTRY  = 'registry-1.docker.io'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Setup') {
            steps {
                sh '''
                pip3 install --upgrade poetry
                poetry config virtualenvs.in-project true
                poetry install --no-interaction --no-root
                '''
            }
        }
        stage('Build') {
            steps {
                sh 'poetry run python -m compileall .'
            }
        }
        stage('Quality') {
            steps {
                sh '''
                poetry run black --check . || true
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                poetry run pytest -v ||true
                '''
            }
        }
    }
}