def registry = 'https://trialhjwbtk.jfrog.io'

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
//         stage("test") {
//             steps{
//                 echo "----------- unit test started ----------"
//                 sh 'mvn surefire-report:report'
//                 echo "----------- unit test Completed ----------"
//             }
//         }
//         stage('SonarQube Analysis') {
//             steps {
//                 withSonarQubeEnv('sonar-server') {
//                     sh "${scannerHome}/bin/sonar-scanner -X"
//                     echo 'SonarQube Analysis Completed'
//                 }
//             }
//         }
//         stage("Quality Gate") {
//             steps {
//                 script {
//                     timeout(time: 1, unit: 'MINUTES') {
//                         def qg = waitForQualityGate()
//                         if (qg.status != 'OK') {
//                             error "Pipeline aborted due to quality gate failure: ${qg.status}"
//                         }
//                     }
//                 }
//             }
//         }
        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'
                }
            }
        }
    }
}