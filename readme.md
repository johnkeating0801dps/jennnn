# Script for scripted pipeline

node {
    stage('Checkout') {
        checkout scm
    }

    stage('Clean') {
        sh 'mvn clean'
    }

    stage('Build and Test') {
        sh 'mvn test'
    }

    stage('Package') {
        sh 'mvn package'
    }

    stage('Archive') {
        archiveArtifacts artifacts: 'target/*.war,target/*.jar',
                         allowEmptyArchive: true
    }
}
