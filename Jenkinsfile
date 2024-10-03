pipeline {
    agent any
    environment {
        scannerHome = tool 'sonar6.1' // Adjust based on your SonarQube version
    }
    stages {
        stage('Prepare Environment') {
            steps {
                echo 'Setting up Python environment...'
                sh 'python3 --version || sudo apt-get install -y python3' // Ensuring Python3 is installed
                sh 'pip3 --version || sudo apt-get install -y python3-pip' // Ensuring pip3 is installed
                sh 'pip3 install --user pytest flake8' // Install pytest, flake8, and other dependencies
                sh 'pip3 install -r requirements.txt' // Install Flask app dependencies
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
                sh 'pytest' // Execute your Python unit tests using pytest
            }
        }
        stage('Checkstyle Analysis') {
            steps {
                echo 'Performing linting with flake8...'
                sh 'flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127' // Static code analysis with flake8
            }
        }
        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar6.1') { // Ensure 'sonar6.1' matches your SonarQube server config
                    sh '''/opt/sonar-scanner/bin/sonar-scanner \
                        -Dsonar.projectKey=flask-app \
                        -Dsonar.projectName=flask-app \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=. \
                        -Dsonar.python.coverage.reportPaths=coverage.xml \
                        -Dsonar.python.pylint.reportPaths=pylint-report.txt'''
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
