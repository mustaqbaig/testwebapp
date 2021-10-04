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
 
