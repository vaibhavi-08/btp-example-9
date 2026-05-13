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
                pip install --upgrade poetry
                poetry config virtualenvs.in-project true
                poetry install --no-interaction
                '''
            }
        }
        stage('Quality') {
            steps {
                sh '''
                . venv/bin/activate || true
                black --check . || true
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                . venv/bin/activate || true
                pip install --upgrade build
                python -m build
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                . venv/bin/activate || true
                pytest -v
                '''
            }
        }
    }
}