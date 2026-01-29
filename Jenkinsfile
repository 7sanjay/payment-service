pipeline {
    agent any

    tools {
        maven 'maven9' // Must match Maven installation in Jenkins
    }

    stages {

        stage('Checkout') {
            steps {
                // Clone your GitHub repo
                git branch: 'main', url: 'https://github.com/7sanjay/payment-service.git'
            }
        }

        stage('Build & Test') {
            steps {
                // Build and run tests
                sh 'mvn clean test'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                // Inject Nexus credentials securely
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds', // Jenkins credential ID
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    script {
                        // Determine release or snapshot version
                        def isRelease = env.BRANCH_NAME == 'main'
                        def versionType = isRelease ? '' : '-SNAPSHOT'
                        def version = "1.0.0${versionType}"

                        echo "Deploying version ${version} to Nexus"

                        // Use settings.xml in repo root
                        sh """
                            mvn deploy \
                                --settings settings.xml \
                                -Drevision=${version} \
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
            echo "Pipeline completed successfully! Artifact deployed to Nexus."
        }
        failure {
            echo "Pipeline failed. Check console logs for details."
        }
    }
}
