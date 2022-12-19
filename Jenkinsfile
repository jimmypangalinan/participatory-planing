def secret = 'nexus02'
def server = 'nexus01@103.13.206.96'
def directory = 'participatory-planing'
def branch = 'master'
def registry = '103.13.206.96:50002/esri-prod/participatory-planing'

pipeline{
        agent any
        stages{
            stage ('compose down and pull'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker-compose down
                    docker system prune -f
                    git pull origin ${branch}
                    exit
                    EOF"""
                }
            }
        }
       stage ('SonarQube Analysis'){
            steps{
                  withSonarQubeEnv(installationName: 'sonarqube-server') {
                  sh 'mvn install:install-file "-Dfile=cobra.jar" "-DgroupId=com.cobra" "-DartifactId=cobra" "-Dversion=0.98.4" "-Dpackaging=jar" "-DgeneratePom=true"'
                  sh 'mvn verify sonar:sonar -Dsonar.host.url=http://103.31.39.128:9000/ -Dsonar.login=69366967208e8615f640ce15711d56bbcdbcc423 -Dsonar.login=admin -Dsonar.password=sonar'
               }
           }
       }
        stage ('build images'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker build -t ${registry}:${BUILD_NUMBER} .
                    exit
                    EOF"""
                 }
             }
        }
        stage ('deploy'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker run -d -t ${registry}:${BUILD_NUMBER}
                    exit
                    EOF"""
                 }
             }
        }
          stage ('push image to registry'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker push ${registry}:${BUILD_NUMBER} 
                    exit
                    EOF"""

              }
          }
        }
     }
  }
