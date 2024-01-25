pipeline {
    agent any
    environment {
        MAVEN_HOME = tool 'Maven'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
        SCANNER_HOME = tool 'Sonar'
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "172.17.0.2:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "maven-central-repo"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "NEXUS_CRED"
        ARTIFACT_VERSION = "${BUILD_NUMBER}"
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
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                     filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;

                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: ARTIFACT_VERSION,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                               // Artifact generated such as .jar, .ear and .war files.
                               [artifactId: pom.artifactId,
                                  classifier: '',
                                  file: artifactPath,
                                  type: pom.packaging]
                               ]
                        );

                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
            }
    }
}
}
}
