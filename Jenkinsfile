def secret = 'nexus02'
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
        stage ('build images'){
            steps{
                sshagent([secret]) {
                    sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker build -t 103.13.206.96:50002/esri-prod/participatory-planing:$BUILD_NUMBER .
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
