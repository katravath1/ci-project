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
        SONARQUBE_SCANNER_HOME = tool 'SonarQube Scanner'
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
                        withSonarQubeEnv('SonarQubeServer') {
                            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=ci-project -Dsonar.host.url=http://172.31.93.121:9000 -Dsonar.login=052bfad244f1760fb2b633cd1bc9985b1151fc57"
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("SonarQube analysis failed: ${e.message}")
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and analysis passed successfully!'
        }
        failure {
            echo 'Build or analysis failed. Please check the logs for details.'
        }
    }
}
