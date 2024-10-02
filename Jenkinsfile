pipeline {
    agent any

    environment {
        APP_NAME = "sample-flask-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/aenisha/Devopsproject.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${APP_NAME}")
                }
            }
        }
        stage('Deploy Application') {
            steps {
                // Assuming you have a Kubernetes deployment setup already
                sh 'kubectl set image deployment/flask-app flask-app=${APP_NAME}'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
