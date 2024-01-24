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
       stage("Publish to Nexus Repository Manager") {
                   steps {
                       script {
                           pom = readMavenPom file: "pom.xml";
                           filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                           echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                           artifactPath = filesByGlob[0].path;
                           artifactExists = fileExists artifactPath;
                           if(artifactExists) {
                               echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                               nexusArtifactUploader(
                                   nexusVersion: nexus3,
                                   protocol: http,
                                   nexusUrl: http://localhost:8081/,
                                   groupId: pom.sn.isi.test,
                                   version: pom.1.0-SNAPSHOT,
                                   repository: maven-central-repo,
                                   credentialsId: NEXUS_CRED,
                                   artifacts: [
                                       [artifactId: pom.projectDevops,
                                       classifier: '',
                                       file: artifactPath,
                                       type: pom.packaging],
                                       [artifactId: pom.artifactId,
                                       classifier: '',
                                       file: "pom.xml",
                                       type: "pom"]
                                   ]
                               );
                           } else {
                               error "*** File: ${artifactPath}, could not be found";
                           }
                       }
                   }
    }
}
