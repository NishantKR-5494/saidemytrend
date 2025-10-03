def registry = 'https://trialxiyig3.jfrog.io/'

pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {
        stage("Build") {
            steps {
                echo "<----- Build Started ----->"
                sh "mvn clean deploy -Dmaven.test.skip=true"
                echo "<----- Build Completed ----->"
            }
        }

        stage("Test") {
            steps {
                echo "<----- Unit Test Started ----->"
                sh "mvn surefire-report:report"
                echo "<----- Unit Test Completed ----->"
            }
        }

        stage("SonarQube Analysis") {
            environment {
                scannerHome = tool "nishant-sonar-scanner"
            }
            steps {
                withSonarQubeEnv("nishant-sonarq-server") {
                    echo "<----- SonarQube Analysis Started ----->"
                    sh "${scannerHome}/bin/sonar-scanner"
                    echo "<----- SonarQube Analysis Completed ----->"
                }
            }
        }

        stage("Jar Publish - JFrog") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started in JFrog --------------->'
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "JFrog_Credentials"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "nishant-libs-release-local/{1}",
                              "flat": "false",
                              "props": "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo '<--------------- Jar Publish Ended in JFrog --------------->'
                }
            }
        }
    }
}
