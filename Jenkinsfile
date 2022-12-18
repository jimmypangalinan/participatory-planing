stage('Code Quality') { 
         script {
         def scannerHome = tool 'sonarqube-prod';
             withSonarQubeEnv("sonarqube-prod") {
             sh "${tool("sonarqube-prod")}/bin/sonar-scanner \
             -Dsonar.projectKey=authorization-sonar \
             -Dsonar.sources=. \
             -Dsonar.host.url=http://103.31.39.128:9000/ \
             -Dsonar.login=088b004c7a5de8ac2bc7c1f7d07e039176c59bf2"
            }
         }
      }
