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
                    def groupId = sh(script: 'mvn help:evaluate -Dexpression=project.groupId -q -DforceStdout', returnStdout: true).trim()
                    def artifactId = sh(script: 'mvn help:evaluate -Dexpression=project.artifactId -q -DforceStdout', returnStdout: true).trim()
                    def version = sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()

                    echo "GroupId: ${groupId}, ArtifactId: ${artifactId}, Version: ${version}"

                    def filesByGlob = findFiles(glob: "target/*.jar")  // Correction ici pour prendre en compte les fichiers JAR
                    if (filesByGlob.size() > 0) {
                        def artifactPath = filesByGlob[0].path
                        echo "*** File: ${artifactPath}, group: ${groupId}, packaging: jar, version ${version}"

                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: 'http://localhost:8081',
                            groupId: 'sn.isi.test',
                            version: '1.0-SNAPSHOT',
                            repository: 'maven-central-repo',
                            credentialsId: 'NEXUS_CRED',
                            artifacts: [
                                [artifactId: projectDevops,
                                classifier: '',
                                file: artifactPath,
                                type: 'jar'],  // Correction ici pour spécifier le type comme 'jar'
                                [artifactId: projectDevops,
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
