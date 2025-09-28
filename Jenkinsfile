pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {
        stage("Build") {
            steps {
                sh "mvn clean deploy"
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
