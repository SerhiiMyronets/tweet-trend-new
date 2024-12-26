def registry = 'https://trialhjwbtk.jfrog.io'
def imageName = 'trialhjwbtk.jfrog.io/artifactory/docker-repo-docker-local/demo-workshop'
def version   = '2.1.2'

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
                     def server = Artifactory.newServer url:registry+"/artifactory",  credentialsId:"jfrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "frog-libs-release-local/{1}",
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

        stage(" Docker Build ") {
          steps {
            script {
               echo '<--------------- Docker Build Started --------------->'
               app = docker.build(imageName+":"+version)
               echo '<--------------- Docker Build Ends --------------->'
            }
          }
        }

        stage (" Docker Publish "){
            steps {
                script {
                    echo '<--------------- Docker Publish Started --------------->'
                    docker.withRegistry(registry, 'jfrog-cred'){
                        app.push()
                    }
                   echo '<--------------- Docker Publish Ended --------------->'
                }
            }
        }
    }
}