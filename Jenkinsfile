pipeline {
    agent any
        tools {
        maven 'mvn'
        jdk 'JDK'
    }
    environment {
       SONAR=' ~/sonar-scanner-3.0.3.778-linux/bin/sonar-scanner'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }
         stage ('Build') {
            steps {
                sh ' mvn -Dmaven.test.failure.ignore=true install '
                
            }
            
        }
        
        stage('Sonar Submission') {
            steps {
                script {
                    if (env.CHANGE_ID) {
                        // Running from a PR
                        branch = env.CHANGE_BRANCH

                        withSonarQubeEnv("SONARQUBE") {
                            // Github plugin for PR's (does not submit to server)
                            sh """${SONAR} -Dsonar.analysis.mode=preview \
                                           -Dsonar.github.pullRequest=${env.CHANGE_ID} \
                                           -Dsonar.github.repository=New_Approach_Demo \
                                           -Dsonar.github.endpoint=https://api.github.com \
                                           
                            """
                        }
                    } else {
                        branch = env.BRANCH_NAME
                    }

                    withSonarQubeEnv("SONARQUBE") {
                        // Submit to sonar server
                        sh "${SONAR} -Dsonar.branch.name=${branch}"
                    }

                    timeout(time: 1, unit: 'HOURS') {
                        def quality_gate = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                        if (quality_gate.status != 'OK') {
                            println "Quality gate failure - marking build as unstable"
                            currentBuild.result = 'UNSTABLE'
                        }
                    }
                }
            }
        }
        
         stage ('Artifactory configuration') {
             
          // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
            
             steps {
                 sh ' mvn deploy '
                  
             }
         }
    }
}
