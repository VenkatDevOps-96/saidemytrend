pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }
    stages {
        stage ('build') {
          steps {
            sh "mvn clean deploy"
            }
          }
        stage ('SonarQube analysis') {
          environment {
            ScannerHome = tool 'saidemy-sonar-scanner'
          }
          steps {
            withSonarQubeEnv('saidemy-sonarqube-server') {
            sh "$(ScannerHome)/bin/sonar-scanner"
            }
          }
       }
    }
}
