pipeline {
    agent any

    tools {
        maven 'maven9'   // Name of Maven installation in Jenkins
    }

    environment {
        // No global credentials here; we'll inject in stage using withCredentials
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
                // Inject Nexus credentials securely
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds',  // Jenkins credential ID
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    script {
                        // Determine version type based on branch
                        def isRelease = env.BRANCH_NAME == 'main'
                        def versionType = isRelease ? '' : '-SNAPSHOT'

                        echo "Deploying version 1.0.0${versionType} to Nexus"

                        sh """
                            mvn deploy \
                              --settings settings.xml \
                              -Drevision=1.0.0${versionType} \
                              -Dnexus.username=$NEXUS_USER \
                              -Dnexus.password=$NEXUS_PASS \
                              -DskipTests
                        """
                    }
                }
            }
        }

    }

    post {
        success {
            echo "Pipeline completed successfully and artifact uploaded to Nexus!"
        }
        failure {
            echo "Pipeline failed. Check console logs for details."
        }
    }
}
