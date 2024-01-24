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
                sh "mvn test"
            }
        }

        stage('Publish to Nexus Repository Manager') {
            steps {
                script {
                    def pom = readMavenPom file: "pom.xml"
                    def filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                    if (filesByGlob.size() > 0) {
                        def artifactPath = filesByGlob[0].path
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}"

                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: 'http://localhost:8081',
                            groupId: 'pom.sn.isi.test',
                            version: '1.0-SNAPSHOT',  // Utilisez la variable pom.version ici si nécessaire
                            repository: 'maven-central-repo',
                            credentialsId: 'NEXUS_CRED',
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        )
                    } else {
                        error "*** No artifact found in target directory"
                    }
                }
            }
        }
    }
}
