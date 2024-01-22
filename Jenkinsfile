pipeline {
    agent any
    environment {
        MAVEN_HOME = tool 'Maven'
        SONAR_HOME = tool 'Sonar'
        PATH = "${MAVEN_HOME}/bin:${SONAR_HOME}/bin:${env.PATH}"
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
                    // Ajoutez votre commande SonarQube ici
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=test-sonar-1 -Dsonar.projectName='test-sonar-1' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_f7335e86463c183ee27e15edf031c4d5d061985a"
                }
            }
        }
    }
}
