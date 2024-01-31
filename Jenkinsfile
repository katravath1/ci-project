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
    }

    stages {
        stage('Build the code') {
            steps {
                script {
                    // Add any specific environment setup or checks here
                    sh 'mvn -s settings.xml -DskipTests clean install -U'
                }
            }
        }
        stage('Junit test for the code'){
            steps {
                sh 'mvn clean compile test'
            }
        }
        stage ('check style analysis of the code') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
    }
    environment {
        SONARQUBE_SCANNER_HOME = tool 'SonarQube Scanner'
    }
        stage ('SonarQube Code Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQubeServer') {
                        sh "mvn clean verify sonar:sonar -Dsonar.projectKey=ci-project
                        Dsonar.host.url=http://172.31.93.121:9000 -Dsonar.login=052bfad244f1760fb2b633cd1bc9985b1151fc57"
                    }
                }
            }
        }
}
