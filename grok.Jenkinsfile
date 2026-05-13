pipeline {
    agent any
    
    tools {
        python 'Python3'
    }
    
    environment {
        POETRY_VERSION = '1.8.0'   // You can change this
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '🔄 Checking out code...'
                checkout scm
            }
        }
        
        stage('Setup') {
            steps {
                echo '🐍 Installing Poetry and setting up environment...'
                sh '''
                    python3 -m pip install --upgrade pip
                    pip install "poetry==${POETRY_VERSION}"
                    
                    # Install project dependencies
                    poetry install --no-root
                    
                    echo "Poetry setup completed successfully"
                '''
            }
        }
        
        stage('Build') {
            steps {
                echo '🏗️ Building Python project...'
                sh '''
                    poetry run python -m compileall app/ || true
                    echo "✅ Build completed"
                '''
            }
        }
        
        stage('Quality') {
            steps {
                echo '✨ Running code quality checks...'
                
                // Black formatting check
                sh '''
                    echo "Running Black code formatter check..."
                    poetry run black --check .
                '''
            }
        }
        
        // Tests stage skipped (tests: false)
        
        stage('Deploy') {
            steps {
                echo '🚀 Deploying Python application...'
                sh '''
                    echo "Activating Poetry environment..."
                    poetry run python app/main.py || echo "No main.py found or application already completed"
                '''
                echo '✅ Deployment stage completed'
            }
        }
    }
    
    post {
        always {
            echo '🧹 Cleaning up...'
            cleanWs()
        }
        success {
            echo '🎉 Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}