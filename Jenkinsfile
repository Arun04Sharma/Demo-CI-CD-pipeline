pipeline {
    agent none

    stages {

        stage('Checkout Code') {
            agent any
            steps {
                checkout scm
            }
        }

        stage('Build JAR') {
    agent any
    tools {
        maven 'Maven'
    }
    steps {
        bat 'mvn clean package -DskipTests'
    }
}

        stage('Docker Build & Push') {
    steps {
        script {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh """
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                docker build -t arun04sharma/deployment:${BUILD_NUMBER} .
                docker push arun04sharma/deployment:${BUILD_NUMBER}
                """
            }
        }
    }
}
    }

    post {
        success {
            echo 'CI pipeline completed successfully'
        }
        failure {
            echo 'CI pipeline failed'
        }
    }
}
