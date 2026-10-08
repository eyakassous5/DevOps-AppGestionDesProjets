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
                        if (isUnix()) { sh 'mvn clean test' } else { bat 'mvn clean test' }
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
                        if (isUnix()) { sh 'mvn package -DskipTests' } else { bat 'mvn package -DskipTests' }
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