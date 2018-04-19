pipeline {
    agent any
        tools {
        maven 'mvn'
        jdk 'JDK'
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
       
         stage ('Artifactory configuration') {
             
          // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
            
             steps {
                 sh ' mvn deploy:deploy'
                  
             }
         }
    }
}
