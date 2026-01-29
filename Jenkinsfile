pipeline {
    agent any

    environment {
        // Nexus credentials stored in Jenkins Credentials Manager
        NEXUS_USER = credentials('nexus-username')
        NEXUS_PASSWORD = credentials('nexus-password')
    }

    tools {
        // Use Maven 9 configured in Jenkins Global Tool Configuration
        maven 'Maven9'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/7sanjay/payment-service.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh '''
                    mvn deploy \
                      --settings settings.xml \
                      -DskipTests
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully with Maven 9!"
        }
        failure {
            echo "Pipeline failed. Check logs for errors."
        }
    }
}
