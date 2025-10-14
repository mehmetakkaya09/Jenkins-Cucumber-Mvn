// pipeline {
//     agent any
//     tools {
//         maven 'MAVEN'
//         jdk 'JDK'
//     }
//     options {
//         timestamps ()
//         ansiColor('gnome-terminal')
//         buildDiscarder(logRotator(numToKeepStr: '10'))
//     }
//
//     triggers {
//         cron('0 6 * * 1-5')
//     }
//
//     parameters {
//         string(name: 'TagName', defaultValue: "@employee", description: 'Scenario Tag to be run')
//     }
//
//     stages {
//         stage('Initialize') {
//             steps {
//                 bat '''
//                     echo "PATH = ${PATH}"
//                     echo "M2_HOME = ${M2_HOME}"
//                 '''
//             }
//         }
//         stage('Build') {
//             steps {
//                 bat "mvn -f pom.xml -B -DskipTests clean package"
//             }
//             post {
//                 success {
// //                     echo "Now Archiving the Artifacts....."
//                     archiveArtifacts artifacts: '**/*.jar'
//                 }
//             }
//         }
//         stage('Test') {
//             steps {
//                 bat "mvn -f pom.xml test"
//                 bat "mvn clean verify -Dcucumber.filter.tags='$params.TagName' -DfailIfNoTests=false"
//             }
//             post {
//                 always {
//         cucumber fileIncludePattern: "**/cucumber.json", jsonReportDirectory: "target/cucumber-reports"
//                 }
//             }
//
//         }
// //         stage('Cucumber Report') {
// //             steps {
// //                 cucumber buildStatus: "UNSTABLE",
// //                     fileIncludePattern: "**/cucumber.json",
// //                     jsonReportDirectory: "target"
// //             }
// //         }
//     }
// }

pipeline {
    agent any

    tools {
        maven 'MAVEN'
        jdk 'JDK'
    }

    options {
        timestamps()
        ansiColor('gnome-terminal')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    triggers {
        cron('0 6 * * 1-5')
    }

    parameters {
        string(name: 'TagName', defaultValue: "@employee", description: 'Scenario Tag to be run')
    }

    stages {

        stage('Initialize') {
            steps {
                bat '''
                    echo "PATH = %PATH%"
                    echo "M2_HOME = %M2_HOME%"
                    mvn -version
                    java -version
                '''
            }
        }

        stage('Build') {
            steps {
                bat "mvn -f pom.xml -B clean package -DskipTests"
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/*.jar'
                }
            }
        }

        stage('Test Execution') {
            steps {
                // Testleri ve Cucumber raporunu çalıştır
                bat "mvn -f pom.xml test"
                bat "mvn clean verify -Dcucumber.filter.tags=${params.TagName} -DfailIfNoTests=false"
            }

            post {
                always {
                    echo " Publishing Cucumber JSON Report..."
                    cucumber fileIncludePattern: "**/cucumber.json", jsonReportDirectory: "target/cucumber-reports"
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Cleaning workspace..."
        }
        failure {
            echo " Pipeline failed."
        }
        success {
            echo " Pipeline completed successfully!"
        }
    }
}
