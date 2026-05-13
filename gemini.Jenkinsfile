pipeline {
    agent any

    environment {
        // Ensuring Poetry creates the virtualenv inside the project folder for easier cleanup
        POETRY_VIRTUALENVS_IN_PROJECT = 'true'
        PATH = "$HOME/.local/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/vaibhavi-08/btp-example-9', branch: 'main'
            }
        }

        stage('Setup') {
            steps {
                script {
                    echo 'Installing Poetry and project dependencies...'
                    sh """
                        # Install poetry if not present
                        curl -sSL https://install.python-poetry.org | python3 -
                        
                        # Install dependencies including Black for quality checks
                        poetry install
                    """
                }
            }
        }

        stage('Quality') {
            steps {
                script {
                    echo 'Running Black code formatter check...'
                    // Black is set to true, so we check for formatting compliance
                    sh "poetry run black --check ."
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building package with Poetry...'
                    sh "poetry build"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Tests are false in config; placeholder provided for Pytest
                    echo 'Tests are disabled. Skipping pytest execution...'
                    // sh "poetry run pytest" 
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deployment stage...'
                    // Define your deployment logic here (e.g., publishing to a private PyPI)
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Project successfully built and verified.'
        }
        failure {
            echo 'Pipeline failed. Please review the Black formatting or build logs.'
        }
    }
}