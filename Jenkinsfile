def secret = 'nexus03'
def server = 'root@103.13.206.96'
def directory = 'participatory-planing'
def branch = 'master'
def registry = '103.13.206.96:50002/esri-prod/participatory-planing'
def scannerHome = 'sonarqube-server'

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
            sshagent([secret]) {
                  sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                  withSonarQubeEnv(installationName: 'sonarqube-server') {
                  sh 'mvn clean install -U -DskipTests -f my-package/pom.xml sonar:sonar -Dsonar.login=61efe6259f9a508a08de6ca9937f335b30f04a4b'
                  exit
                  EOF"""
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
