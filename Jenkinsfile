pipeline {
    agent any
    environment {
        scannerHome = tool 'sonar6.1' // Adjust based on your SonarQube version
    }
    stages {
        stage('Prepare Environment') {
            steps {
                echo 'Setting up Python virtual environment...'
                sh '''
                    python3 -m venv venv   # Create a virtual environment
                    . venv/bin/activate    # Activate the virtual environment
                    pip install --upgrade pip  # Upgrade pip in virtual environment
                    pip install pytest flake8  # Install dependencies
                    pip install -r requirements.txt  # Install any app-specific dependencies
                '''
            }
        }
        stage('Build') {
            when {
                branch 'main' // Build only on 'main' branch
            }
            steps {
                echo 'Building Flask App'
            }
            post {
                success {
                    echo 'Archiving Flask App Python files...'
                    archiveArtifacts artifacts: '**/*.py'
                }
            }
        }
        stage('UNIT TEST') {
            steps {
                echo 'Running unit tests...'
                sh '''
                    . venv/bin/activate  # Activate the virtual environment
                    pytest  # Run tests
                '''
            }
        }
        stage('Checkstyle Analysis') {
            steps {
                echo 'Performing linting with flake8...'
                sh '''
                    . venv/bin/activate  # Activate the virtual environment
                    flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127
                '''
            }
        }
        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar6.1') { // Ensure 'sonar6.1' matches your SonarQube server config
                    sh '''
                        . venv/bin/activate  # Activate the virtual environment
                        /opt/sonar-scanner/bin/sonar-scanner \
                            -Dsonar.projectKey=flask-app \
                            -Dsonar.projectName=flask-app \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=. \
                            -Dsonar.python.coverage.reportPaths=coverage.xml \
                            -Dsonar.python.pylint.reportPaths=pylint-report.txt
                    '''
                }
            }
        }
    }
    post {
        always {
            cleanWs() // Clean up workspace after build
        }
    }
}
