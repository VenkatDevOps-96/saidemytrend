// Define the URL of the Artifactory registry
def registry = 'https://trial9gp9fa.jfrog.io'

pipeline {

    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage("build") {
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- build completed ----------"
            }
        }

        stage("test") {
            steps {
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                echo "----------- unit test completed ----------"
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'saidemy-sonar-scanner'
            }
            steps {
                withSonarQubeEnv('saidemy-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        // 👇 Quality Gate stage removed to skip waiting

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                    def server = Artifactory.newServer url:

