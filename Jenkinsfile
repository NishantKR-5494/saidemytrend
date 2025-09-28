pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {
        stage("Build") {
            steps {
                echo "----- Build Started -----"
                sh "mvn clean deploy -Dmaven.test.skip=true"
                echo "----- Build Completed -----"
            }
        }

        stage("Test") {
            steps {
                echo "----- Unit Test Started -----"
                sh "mvn surefire-report:report"
                echo "----- Unit Test Completed -----"
            }
        }

        stage("SonarQube Analysis") {
            environment {
                scannerHome = tool "nishant-sonar-scanner"
            }
            steps {
                withSonarQubeEnv("nishant-sonarq-server") {

                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
