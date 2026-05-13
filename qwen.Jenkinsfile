pipeline {
    agent any

    environment {
        PYTHON_VERSION = '3.9'
        POETRY_VERSION = '1.7.1'
        VENV_PATH = '.venv'
        BLACK_CHECK_ARGS = '--check --diff --color'
    }

    stages {
        // Stage 1: Checkout source code from GitHub
        stage('Checkout') {
            steps {
                checkout scm
                echo "✅ Checked out repository: ${env.GIT_URL}"
                // Verify Poetry config files exist
                sh 'ls -la pyproject.toml poetry.lock || echo "⚠️ Poetry files not found"'
            }
        }

        // Stage 2: Setup Python environment with Poetry
        stage('Setup') {
            steps {
                script {
                    sh """
                        # Install Poetry if not available
                        if ! command -v poetry &> /dev/null; then
                            echo "📦 Installing Poetry ${POETRY_VERSION}..."
                            curl -sSL https://install.python-poetry.org | python${PYTHON_VERSION} - --version ${POETRY_VERSION}
                            export PATH="\$HOME/.local/bin:\$PATH"
                        fi
                        
                        # Configure Poetry to create venv in project
                        poetry config virtualenvs.in-project true
                        
                        # Install dependencies from poetry.lock (exact versions)
                        echo "🔐 Installing dependencies via Poetry..."
                        poetry install --no-interaction --no-ansi
                        
                        # Activate virtualenv for subsequent stages
                        echo "✅ Poetry environment ready: ${VENV_PATH}"
                    """
                }
            }
        }

        // Stage 3: Quality checks - Black code formatting (ENABLED) ✅
        stage('Quality') {
            steps {
                script {
                    sh """
                        # Run Black in check mode (no auto-formatting)
                        poetry run black ${BLACK_CHECK_ARGS} .
                        echo "✅ Black formatting check passed"
                    """
                }
            }
            post {
                failure {
                    echo "❌ Black formatting issues found!"
                    echo "💡 Run 'poetry run black .' locally to auto-fix formatting"
                }
            }
        }

        // Stage 4: Build - Validate Python syntax and project structure
        stage('Build') {
            steps {
                script {
                    sh """
                        # Validate pyproject.toml syntax
                        poetry check --lock
                        
                        # Compile Python bytecode to catch syntax errors
                        poetry run python -m compileall -q .
                        
                        echo "✅ Build completed: Project structure and syntax validated"
                    """
                }
            }
        }

        // Stage 5: Test execution - SKIPPED per configuration
        // tests: "false" → Stage disabled (pytest configured but not executed)
        /*
        stage('Test') {
            steps {
                script {
                    sh """
                        poetry run pytest tests/ \
                            --cov=app \
                            --cov-report=xml \
                            --junitxml=test-reports/junit.xml
                    """
                }
                post {
                    always {
                        junit testResults: 'test-reports/*.xml', allowEmptyResults: true
                    }
                }
            }
        }
        */

        // Stage 6: Deploy - SKIPPED per configuration
        // Dockerfile: "false" → No containerization configured
        /*
        stage('Deploy') {
            when {
                branch 'master'
            }
            steps {
                script {
                    echo "ℹ️ Deployment disabled - no Dockerfile configured"
                    // Future: Add poetry build + docker/pip deploy steps
                }
            }
        }
        */
    }

    post {
        always {
            // Cleanup build artifacts to save workspace
            sh 'rm -rf .venv __pycache__ **/__pycache__ .pytest_cache htmlcov'
            echo "🧹 Workspace cleaned"
        }
        success {
            echo "🎉 Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed - check console output for details"
            // Optional: Add notification logic (email/Slack) here
        }
    }
}