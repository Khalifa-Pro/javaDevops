pipeline {
    agent any
    environment {
        MAVEN_HOME = tool 'Maven'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
        SCANNER_HOME = tool 'Sonar'
    }
    stages {
        stage('Build') {
            steps {
                sh "mvn package sonar:sonar"
            }
        }
        stage('Test') {
            steps {
                sh "mvn compile"
            }
        }
    }
}
