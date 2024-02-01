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
        stage ('upload to Nexus') {
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${RELEASE_REPO}",
                  credentialsId: "${NEXUS_LOGIN}",
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }
        stage ('clean workspace in the jenkins server') {
            steps {
                cleanWs()
            }
        }
    }

}
