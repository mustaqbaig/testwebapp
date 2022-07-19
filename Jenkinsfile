pipeline {
  agent any
  tools {
  maven 'maven1'
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
    
    //stage ('SAST') {
      //steps {
       //  withSonarQubeEnv('sonar') {
         // sh 'mvn sonar:sonar'
          //sh 'cat target/sonar/report-task.txt'
    //    }
     // }
    //}
       stage ('Build') {
      steps {
      sh 'mvn clean package -e'
       }
    }
    
   stage ('Deploy-To-Tomcat') {
         steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.88.155.32:/opt/tomcat/webapps/webapp.war'
          }      
      }       
    }
    
    
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@172.31.28.9 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://172.31.26.96:8080/webapp/" || true'
        }
     }
   }
  }
  }
