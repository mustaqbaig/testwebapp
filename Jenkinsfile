pipeline {
  agent any
  tools {
  maven 'Maven'
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
    
    stage ('Check-git-Secrets') {
      steps {
      sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/imran209/webapp.git > trufflehog '
        sh 'cat trufflehog'
      }
    }
    stage('source composition analysis') {
      steps{
     sh ' rm owasp* || true'
        sh ' wget "https://raw.githubusercontent.com/imran209/webapp/master/owasp-dependency-check.sh" '
        sh ' chmod +x owasp-dependency-check.sh'
        sh ' bash owasp-dependency-check.sh'
      }
    }
    
    stage ('Build') { 
    steps {
      sh 'mvn clean package'
       }
    }
     stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.126.132.77:/prod/apache-tomcat-9.0.53/webapps/webapp.war'
              }      
           } 
  }
}
}
 
