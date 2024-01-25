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
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=test-sonar-3 -Dsonar.projectName='test-sonar-3' -Dsonar.host.url=http://172.17.0.3:9000 -Dsonar.token=sqp_035db7fd7c970fd1991511f1a1c550952833cbae"
                }
            }
        }
        stage('Upload artefact project to Nexus Repository Manager') {
            steps {
                nexusArtifactUploader artifacts: [
                [
                    artifactId: 'projectDevops',
                    classifier: '',
                    file: 'target/projectDevops-1.0-SNAPSHOT.jar',
                    type: 'jar'
                ]],
                credentialsId: 'NEXUS_CRED',
                groupId: 'sn.isi.test',
                protocol: 'http',
                nexusUrl: '127.0.0.1:8081',
                nexusVersion: 'nexus2',
                repository: 'http://127.0.0.1:8081/repository/maven-central-repo/',
                version: '1.0-SNAPSHOT'
            }
        }
    }
}
