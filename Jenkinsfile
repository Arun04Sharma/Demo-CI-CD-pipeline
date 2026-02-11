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
            agent {
                docker {
                    image 'maven:3.9.6-eclipse-temurin-17'
                    // This allows the Maven container to also see the Docker socket if needed
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build & Push') {
            agent any 
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker build -t arun04sharma/deployment:${env.BUILD_NUMBER} .
                        docker push arun04sharma/deployment:${env.BUILD_NUMBER}
                        """
                    }
                }
            }
        }
    }

    post {
        success { echo 'CI pipeline completed successfully' }
        failure { echo 'CI pipeline failed' }
    }
}