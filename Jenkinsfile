
pipeline {
    agent any

    tools {
        maven 'maven9'   // Maven installation name in Jenkins
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
                    configFileProvider([configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]) {
                        script {
                            def isRelease = env.BRANCH_NAME == 'main'
                            def versionType = isRelease ? '' : '-SNAPSHOT'
                            def version = "1.0.0${versionType}"
                            
                            echo "Deploying version ${version} to Nexus"

                            // Maven deploy using settings.xml
                            sh "mvn deploy --settings $MAVEN_SETTINGS -Drevision=${version} -DskipTests"
                        }
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
            echo "Pipeline failed. Check logs for details."
        }
    }
}
