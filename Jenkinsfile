pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'eyakassous5'
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/gestion-projets-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/gestion-projets-frontend"
        IMAGE_TAG      = "${BUILD_NUMBER}"
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Tests unitaires') {
            steps {
                dir('backend') {
                    sh 'chmod +x mvnw && ./mvnw clean test'
                }
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'backend/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Build / Livrable') {
            steps {
                dir('backend') {
                    sh './mvnw package -DskipTests'
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
                }
            }
        }

        stage('Build images Docker') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE:$IMAGE_TAG -t $BACKEND_IMAGE:latest ./backend'
                sh 'docker build -t $FRONTEND_IMAGE:$IMAGE_TAG -t $FRONTEND_IMAGE:latest ./frontend'
            }
        }

        stage('Push Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                  usernameVariable: 'DH_USER',
                                                  passwordVariable: 'DH_PASS')]) {
                    sh 'echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin'
                    sh 'docker push $BACKEND_IMAGE:$IMAGE_TAG'
                    sh 'docker push $BACKEND_IMAGE:latest'
                    sh 'docker push $FRONTEND_IMAGE:$IMAGE_TAG'
                    sh 'docker push $FRONTEND_IMAGE:latest'
                }
            }
            post {
                always {
                    sh 'docker logout'
                }
            }
        }
    }
}