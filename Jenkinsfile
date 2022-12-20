def secret = 'awsdocker'
def server = 'dockeradmin@52.221.192.202'
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
                    git checkout ${branch}
                    git pull origin ${branch}
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
                    docker run --name participatory-planing-prod -d -p 3002:3001 -t  ${registry}:${BUILD_NUMBER}
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
         stage ('remove old image '){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker rmi -f ${registry}:${BUILD_NUMBER}
                    exit
                    EOF"""
              }
           }
        }
     }
  }
