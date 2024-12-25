pipeline {
//     agent {
//         node {
//             label "slave"
//         }
//     }
    agent any
    environment {
        PATH = "/opt/apache-maven-3.9.2/bin:$PATH"
        scannerHome = tool 'sonar-scanner'
    }
    stages {
        stage("build") {
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- build completed ----------"
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "${scannerHome}/bin/sonar-scanner -X"
                    echo 'SonarQube Analysis Completed'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 1, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
//         stage("test") {
//             steps{
//                 echo "----------- unit test started ----------"
//                 sh 'mvn surefire-report:report'
//                 echo "----------- unit test Completed ----------"
//             }
//         }
    }
}