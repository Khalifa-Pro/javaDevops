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
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=test-sonar-2 -Dsonar.projectName='test-sonar-2' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_24709487bff13bed91f51ebebece339e89038e54"
                }
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
                            groupId: pom.groupId,
                            version: pom.version,
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
