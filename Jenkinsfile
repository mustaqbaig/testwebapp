pipeline {
  agent any
  tools {
  maven 'maven'
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
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@172.31.26.96:/opt/tomcat/webapps/webapp.war'
              }      
           }       
    }
    
    
   // stage ('DAST') {
     // steps {
       // sshagent(['zap']) {
        // sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.232.158.44 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://13.232.202.25:8080/webapp/" || true'
    //    }
      // }
    // }
  }
  }
