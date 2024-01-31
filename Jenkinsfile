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
}
