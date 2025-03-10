pipeline {
    agent any
    stages {
        stage("Build") {
            steps {
                sh "mvn clean deploy"
            }
        }

        stage("SonarQube Code Analysis") {
            environment {
                scannerHome = tool "sonarqube"
            }
            steps {
                withSonarQubeEnv(credentialsId: 'sonarqube-credentials') {
                    sh "${$scannerHome}/bin/sonar-scanner"
                }
            }
        }
        
    }
}
