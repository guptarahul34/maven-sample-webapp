def registry = 'https://trial33hhmz.jfrog.io'

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

        stage("Test") {
            steps {
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                echo "----------- unit test completed ----------"
            }
        }

        stage("SonarQube Code Analysis") {
            environment {
                scannerHome = tool "sonarqube"
            }
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage("Jar Publish") {                // 14  // Creates a stage named 'Jar Publish'
            steps {                           // 15  // Defines the steps that will be executed in this stage
                script {                      // 16  // Allows running custom Groovy script inside the pipeline
                    echo '<--------------- Jar Publish Started --------------->'  
                                              // Logs a message indicating the start of JAR publishing
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "jfrog-credentials"  
                                              // Defines the Artifactory server with the specified URL and credentials
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"  
                                              // Sets properties like build ID and Git commit ID for the build
                    def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "target/*.war",
                              "target": "devops-learning-libs-release-local/",
                              "flat": "false",
                              "props": "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""  
                                              // Defines the upload specification for uploading JAR files to Artifactory
                    def buildInfo = server.upload(uploadSpec)  
                                              // Uploads the files to Artifactory and collects build info
                    buildInfo.env.collect()  
                                              // Collects environment variables as part of the build info
                    server.publishBuildInfo(buildInfo)  
                                              // Publishes the build information to Artifactory
                    echo '<--------------- Jar Publish Ended --------------->'  
                                              // Logs a message indicating the end of JAR publishing
                }                             // 16  // Ends the script block for 'Jar Publish' stage
            }                                 // 15  // Ends the steps block for 'Jar Publish' stage
        } 
        
    }
}
