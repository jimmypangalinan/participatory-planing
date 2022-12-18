def secret = 'nexus01'
def server = 'nexus01@103.13.206.96'
def directory = 'participatory-planing'
def branch = 'master'

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
        stage('Code Quality') { 
        steps{
         script {
         def scannerHome = tool 'test';
             withSonarQubeEnv("test") {
             sh "${tool("test")}/bin/sonar-scanner \
             -Dsonar.projectKey=test \
             -Dsonar.sources=. \
             -Dsonar.host.url=http://103.31.39.128:9000/ \
             -Dsonar.login=088b004c7a5de8ac2bc7c1f7d07e039176c59bf2"
            }
         }
      }
        stage ('build images'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker build -t 103.13.206.96:50002/esri-prod/participatory-planing:$BUILD_NUMBER
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
                    docker run -t 103.13.206.96:50002/esri-prod/participatory-planing:$BUILD_NUMBER
                    exit
                    EOF"""
                }
            }
        }
    }
}
