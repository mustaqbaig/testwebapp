pipeline {
  agent any
  tools {
  maven 'maven1'
  }
  stages {
    stage ('Commit') {
      steps {
      sh '''
                    echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
            ''' 
     }
    }
    stage ('Check-Secrets-Leakage') {
      steps {
        sh 'rm trufflehog || true'
      //  sh 'docker run gesellix/trufflehog --regex --entropy FALSE --json https://github.com/mustaqbaig/mutillidae.git > trufflehog'
        sh 'trufflehog --regex --entropy FALSE  https://github.com/webpwnized/mutillidae > threort1'
        sh 'cat threort1'
   
      }
     } 
    
     stage ('Software Composition Analysis'){
         steps{
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/mustaqbaig/testwebapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
         }
     }
    
    stage ('SAST') {
      steps {
         withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
     }
    }
    
       stage ('Build') {
      steps {
      sh 'mvn clean package -e'
       }
    }
    
   stage ('Deploy-To-Tomcat') {
         steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.146.224.144:/opt/tomcat/webapps/webapp.war'
          }      
      }       
    }
    
     stage ('CCA') {
      steps {
       
         sh 'curl -X GET "http://13.92.228.70:8080/cca-prod/audit/execute/884" -H "accept: */*" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZ25pQHJwb2xpLmNvbSIsImV4cCI6MTY2MjAyMDc2NiwiYWN0aW9ucyI6W3siYXV0aG9yaXR5IjoiQWNjZXB0X0F1ZGl0In0seyJhdXRob3JpdHkiOiJBZGRfQXVkaXQifSx7ImF1dGhvcml0eSI6IkFkZF9DbG91ZCJ9LHsiYXV0aG9yaXR5IjoiQWRkX0VudiJ9LHsiYXV0aG9yaXR5IjoiQWRkX3RlbXBsYXRlcyJ9LHsiYXV0aG9yaXR5IjoiQXNzaWduX0F1ZGl0In0seyJhdXRob3JpdHkiOiJDb21tZW50X0F1ZGl0In0seyJhdXRob3JpdHkiOiJDb21wcmVoZW5zaXZlX1J1bl9BdWRpdCJ9LHsiYXV0aG9yaXR5IjoiQ3JlYXRlX1VzZXIifSx7ImF1dGhvcml0eSI6IkRlbGV0ZV9BdWRpdCJ9LHsiYXV0aG9yaXR5IjoiRGVsZXRlX1VzZXIifSx7ImF1dGhvcml0eSI6IkVkaXRfQXVkaXQifSx7ImF1dGhvcml0eSI6IkVkaXRfVXNlciJ9LHsiYXV0aG9yaXR5IjoiUXVpY2tfUnVuX0F1ZGl0In0seyJhdXRob3JpdHkiOiJSZWplY3RfQXVkaXQifSx7ImF1dGhvcml0eSI6IlZpZXdfQXVkaXQifV0sInVzZXIiOiJ7XCJpZFwiOjEsXCJjcmVhdGVkT25cIjoxNjI5NDM3NDQ3MDAwLFwiZW1haWxcIjpcImFnbmlAcnBvbGkuY29tXCIsXCJmaXJzdE5hbWVcIjpcIkFnbmlob3RyYVwiLFwibGFzdE5hbWVcIjpcIkFkbWluXCIsXCJuYW1lXCI6XCJBZ25pXCIsXCJ1cGRhdGVkT25cIjoxNjI5ODkxNjc3MDAwLFwiY2xpZW50XCI6e1wiaWRcIjoyLFwiY29udGFjdFwiOlwiOTQ5MTUyMDIwMFwiLFwibmFtZVwiOlwiUlBPTElcIixcInBvY0VtYWlsXCI6XCJhZ25pQHJwb2xpLmNvbVwiLFwicG9jTmFtZVwiOlwiRGV2UE9DXCJ9LFwiY3JlYXRlZEJ5XCI6MSxcInVwZGF0ZWRCeVwiOjEsXCJ1c2VyUm9sZVwiOntcImlkXCI6MSxcIm5hbWVcIjpcIkFkbWluaXN0cmF0b3JcIixcInJvbGVBY3Rpb25NYXBzXCI6W3tcImlkXCI6MjMsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjo3LFwibmFtZVwiOlwiVmlld19BdWRpdFwifX0se1wiaWRcIjoyNSxcInJvbGVJZFwiOjEsXCJhY3Rpb25cIjp7XCJpZFwiOjksXCJuYW1lXCI6XCJSZWplY3RfQXVkaXRcIn19LHtcImlkXCI6MjYsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjoxMCxcIm5hbWVcIjpcIkVkaXRfQXVkaXRcIn19LHtcImlkXCI6MjcsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjoxMSxcIm5hbWVcIjpcIkNvbW1lbnRfQXVkaXRcIn19LHtcImlkXCI6MjgsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjoxMixcIm5hbWVcIjpcIkFzc2lnbl9BdWRpdFwifX0se1wiaWRcIjoyOSxcInJvbGVJZFwiOjEsXCJhY3Rpb25cIjp7XCJpZFwiOjEzLFwibmFtZVwiOlwiRGVsZXRlX0F1ZGl0XCJ9fSx7XCJpZFwiOjMwLFwicm9sZUlkXCI6MSxcImFjdGlvblwiOntcImlkXCI6MTQsXCJuYW1lXCI6XCJRdWlja19SdW5fQXVkaXRcIn19LHtcImlkXCI6MzEsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjoxNSxcIm5hbWVcIjpcIkNvbXByZWhlbnNpdmVfUnVuX0F1ZGl0XCJ9fSx7XCJpZFwiOjMyLFwicm9sZUlkXCI6MSxcImFjdGlvblwiOntcImlkXCI6MTYsXCJuYW1lXCI6XCJBZGRfQ2xvdWRcIn19LHtcImlkXCI6MzQsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjoxOCxcIm5hbWVcIjpcIkFkZF9BdWRpdFwifX0se1wiaWRcIjozNSxcInJvbGVJZFwiOjEsXCJhY3Rpb25cIjp7XCJpZFwiOjE5LFwibmFtZVwiOlwiQWRkX3RlbXBsYXRlc1wifX0se1wiaWRcIjozNixcInJvbGVJZFwiOjEsXCJhY3Rpb25cIjp7XCJpZFwiOjIwLFwibmFtZVwiOlwiQ3JlYXRlX1VzZXJcIn19LHtcImlkXCI6MzcsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjoyMSxcIm5hbWVcIjpcIkVkaXRfVXNlclwifX0se1wiaWRcIjozOCxcInJvbGVJZFwiOjEsXCJhY3Rpb25cIjp7XCJpZFwiOjIyLFwibmFtZVwiOlwiRGVsZXRlX1VzZXJcIn19LHtcImlkXCI6NDQsXCJyb2xlSWRcIjoxLFwiYWN0aW9uXCI6e1wiaWRcIjo4LFwibmFtZVwiOlwiQWNjZXB0X0F1ZGl0XCJ9fSx7XCJpZFwiOjQ1LFwicm9sZUlkXCI6MSxcImFjdGlvblwiOntcImlkXCI6MTcsXCJuYW1lXCI6XCJBZGRfRW52XCJ9fV19fSIsImlhdCI6MTY2MjAxNzE2Nn0.9-_1Py3DENl6Tnf3i7i50ErzbB50SAAbO84lmm3d4ko"'
        }
     }

  
    
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@172.31.28.9 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://54.146.224.144:8080/webapp/" || true'
        }
     }

    }
    
   stage ('Port & Service Scan') {
      steps {
      //  sh 'chmod +x /home/ubuntu/Nettacker/nettacker.py'
    //     sh 'python3 /home/ubuntu/Nettacker/nettacker.py -i 54.146.224.144 -m port_scan -o /home/ubuntu/Nettacker/Report.json'
        sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/mustaqbaig/testwebapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        }
     }

 
  
 
  }
}
