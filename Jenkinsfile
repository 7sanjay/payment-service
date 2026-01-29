pipeline {
    agent any

    tools {
        maven 'maven9'   // Must match Maven installation name in Jenkins
    }

    environment {
        // Optionally set Maven local repo to workspace to avoid permission issues
        MAVEN_OPTS = "-Dmaven.repo.local=${WORKSPACE}/.m2/repository"
    }

    stages {

        stage('Checkout') {
            steps {
                // Clone your GitHub repo
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/7sanjay/payment-service.git']]
                ])
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
                        // Determine if release or snapshot based on branch
                        def isRelease = env.BRANCH_NAME == 'main'
                        def versionType = isRelease ? '' : '-SNAPSHOT'
                        def version = "1.0.0${versionType}"

                        echo "Deploying version ${version} to Nexus"

                        // Maven deploy
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
