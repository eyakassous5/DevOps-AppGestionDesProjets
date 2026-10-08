pipeline {
    agent any

    triggers {
        githubPush()
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
                    script {
                        if (isUnix()) {
                            sh 'chmod +x mvnw && ./mvnw clean test'
                        } else {
                            bat 'mvnw.cmd clean test'
                        }
                    }
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
                    script {
                        if (isUnix()) {
                            sh './mvnw package -DskipTests'
                        } else {
                            bat 'mvnw.cmd package -DskipTests'
                        }
                    }
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
                }
            }
        }
    }
}