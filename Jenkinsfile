pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk 'OracleJDK8'
    }
    stages {
        stage('build the code'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}