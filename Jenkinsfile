pipeline {
    agent none
    
    environment {
        APP_VERSION = "1.0" 
        IMAGE_TAG = "${APP_VERSION}.${BUILD_NUMBER}"
    }

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
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker build -t sharma04arun/deployment:${IMAGE_TAG} .
                            docker push sharma04arun/deployment:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Update Helm Tag') {
            agent any
            steps {
                script {
                    // Git setup
                    sh "git config user.email 'jenkins@example.com'"
                    sh "git config user.name 'Jenkins-CI'"

                    // Make sure 'values.yaml' path is correct according to your repo structure
                    // Agar values.yaml root mein hai toh sirf 'values.yaml' likhna
                    sh "sed -i 's/tag: .*/tag: \"${IMAGE_TAG}\"/' demo-app/values.yaml"

                    sh "git add values.yaml"
                    sh "git commit -m 'Update image tag to ${IMAGE_TAG} [skip ci]'"
                    
                    withCredentials([usernamePassword(
                        credentialsId: 'github-creds', 
                        passwordVariable: 'GIT_PASSWORD', 
                        usernameVariable: 'GIT_USERNAME'
                    )]) {
                        // Corrected GitHub URL structure
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Arun04Sharma/Demo-CI-CD-pipeline.git HEAD:main"
                    }
                }
            }
        }
    }
}