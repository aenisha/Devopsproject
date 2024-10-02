pipeline {
    agent any

    environment {
        REGISTRY = "localhost:5000"
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
                    dockerImage = docker.build("${REGISTRY}/${APP_NAME}")
                }
            }
        }
        stage('Push to Local Registry') {
            steps {
                script {
                    docker.withRegistry("http://${REGISTRY}", '') {
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy Application') {
            steps {
                sh 'kubectl set image deployment/flask-app flask-app=localhost:5000/sample-flask-app:latest'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
