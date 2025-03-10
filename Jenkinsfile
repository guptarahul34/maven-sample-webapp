pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage("Build") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("SonarQube Code Analysis") {
            environment {
                scannerHome = tool "sonarqube"
            }
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh "${$scannerHome}/bin/sonar-scanner"
                }
            }
        }
        
    }
}
