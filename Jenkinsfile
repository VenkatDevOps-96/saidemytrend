pipeline {                                    // 1  // Defines the start of the Jenkins pipeline block

    agent any                                 // Specifies the pipeline can run on any available agent

    environment {                             // 2  // Defines environment variables for the pipeline
        PATH = "/opt/maven/bin:$PATH"         // Adds Maven's path to the system's PATH variable
    }                                         // 2  // Ends the environment block

    stages {                                  // 3  // Defines the stages block where multiple stages are declared

        stage("Test") {                       // 4  // Stage to run unit tests
            steps {                           // 5
                echo "----------- Unit tests started ----------"
                sh 'mvn test'
                echo "----------- Unit tests completed ----------"
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'  // Publishes test reports to Jenkins
                }
            }
        }                                     // 4  // Ends the 'Test' stage

        stage("Build & Deploy") {             // 6  // Combines build and deploy steps
            steps {
                echo "----------- Build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build completed ----------"
            }
        }                                     // 6  // Ends the 'Build & Deploy' stage

        stage("SonarQube Analysis") {         // 7
            environment {
                scannerHome = tool 'saidemy-sonar-scanner'     // SonarQube scanner tool
            }
            steps {
                withSonarQubeEnv('saidemy-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }                                     // 7  // Ends the 'SonarQube Analysis' stage

        stage("Quality Gate") {               // 8
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            // Option 1: Just warn
                            echo "Warning: Quality Gate failed but continuing pipeline: ${qg.status}"

                            // Option 2: Fail the pipeline (Uncomment below to enforce)
                            // error "Pipeline aborted due to failed Quality Gate: ${qg.status}"
                        }
                    }
                }
            }
        }                                     // 8  // Ends the 'Quality Gate' stage
    }                                         // 3  // Ends the stages block
}                                             // 1  // Ends the pipeline block

