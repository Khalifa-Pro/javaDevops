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
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=test-sonar-2 -Dsonar.projectName='test-sonar-2' -Dsonar.host.url=http://172.17.0.2:9000 -Dsonar.token=sqp_24709487bff13bed91f51ebebece339e89038e54"
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
                nexusUrl: '172.17.0.4',
                nexusVersion: 'nexus2',
                protocol: 'http',
                repository: 'http://localhost:8081/repository/maven-central-repo/',
                version: '1.0-SNAPSHOT'
            }
        }
    }
}
