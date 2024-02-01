pipeline {
    agent any
    
    tools {
        maven "MAVEN3"
        jdk 'OracleJDK8'
    }

    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'shiva123'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.87.232'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARQUBE_SCANNER_HOME = tool 'sonarscanner'
        NEXUS_CREDENTIALS_ID = 'nexuslogin'
        NEXUS_REPO_URL = 'http://172.31.87.232:8081/repository/vprofile-release'
        ARTIFACT_FILE = '/var/lib/jenkins/workspace/ci-jenkins/target/vprofile-v2.war'
        GROUP_ID = 'vpro-maven-group'
        ARTIFACT_ID = 'spring-test'
        VERSION = '1.0.0'
    }

    stages {
        stage('Build the code') {
            steps {
                script {
                    try {
                        sh 'mvn -s settings.xml -DskipTests clean install -U'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Build failed: ${e.message}")
                    }
                }
            }
        }

        stage('Junit test for the code') {
            steps {
                script {
                    try {
                        sh 'mvn clean compile test'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("JUnit tests failed: ${e.message}")
                    }
                }
            }
        }

        stage('Checkstyle analysis of the code') {
            steps {
                script {
                    try {
                        sh 'mvn checkstyle:checkstyle'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Checkstyle analysis failed: ${e.message}")
                    }
                }
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                script {
                    try {
                        withSonarQubeEnv('sonarserver') {
                            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=ci-project -Dsonar.host.url=http://172.31.93.121:9000 -Dsonar.login=4881bb2f7a76351c9857c6398eced3fed493f283"
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("SonarQube analysis failed: ${e.message}")
                    }
                }
            }
        }
        stage('check quality gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('publish to Nexus') {
            steps {
                script {
                    def nexusArtifactUploader = nexusArtifactUploader()

                    nexusArtifactUploader.credentialsId = env.NEXUS_CREDENTIALS_ID
                    nexusArtifactUploader.repositoryUrl = env.NEXUS_REPO_URL
                    nexusArtifactUploader.artifact = env.ARTIFACT_FILE
                    nexusArtifactUploader.groupId = env.GROUP_ID
                    nexusArtifactUploader.artifactId = env.ARTIFACT_ID
                    nexusArtifactUploader.version = env.VERSION

                    nexusArtifactUploader.deploy()
                }
            }
        }
    }

}
