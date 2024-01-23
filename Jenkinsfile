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
                sh "mvn clean install"
            }
        }
        stage('Test') {
            steps {
                sh "mvn compile"
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=test-sonar-2 -Dsonar.projectName='test-sonar-2' -Dsonar.host.url=http://172.17.208.1:9000 -Dsonar.token=sqp_24709487bff13bed91f51ebebece339e89038e54"
                }
            }
        }
    }
}
