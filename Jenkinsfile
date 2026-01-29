pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/7sanjay/payment-service.git'
      }
    }

    stage('Build & Test') {
      steps {
        sh 'mvn clean test'
      }
    }

    stage('Deploy to Nexus') {
      steps {
        sh 'mvn deploy -DskipTests'
      }
    }
  }
}
