pipeline {
    agent any

    tools {
        maven 'maven9' // Make sure this matches your Jenkins Maven installation name
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/7sanjay/payment-service.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds', // your Jenkins Nexus credential ID
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh """
                        mvn deploy \
                            --settings settings.xml \
                            -Drevision=1.0.0-SNAPSHOT \
                            -Dnexus.username=$NEXUS_USER \
                            -Dnexus.password=$NEXUS_PASS \
                            -DskipTests
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! Artifact deployed."
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
