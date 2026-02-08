pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t demo-app:1.0 .'
            }
        }
    }

    post {
        success {
            echo 'Build and Docker image creation successful'
        }
        failure {
            echo 'Build failed'
        }
    }
}
