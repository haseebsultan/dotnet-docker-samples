 def dockerrun = 'docker run -p 8000:80 -d --name sampledotnet haseebsultan/dot_net_descriptive_pipeline:latest'
 def dockerrm = 'docker container rm -f sampledotnet'
 def dockerimagerm = 'docker image rmi -f haseebsultan/dot_net_descriptive_pipeline'


pipeline{
    agent any
    stages{
       
       stage("Build Docker File"){
          steps{
                sh 'docker image build -t $JOB_NAME:v2.$BUILD_ID aspnetapp/.'
                sh 'docker image tag $JOB_NAME:v2.$BUILD_ID haseebsultan/$JOB_NAME:v2.$BUILD_ID'
                sh 'docker image tag $JOB_NAME:v2.$BUILD_ID haseebsultan/$JOB_NAME:latest'
               }

            }
        stage("Push image to docker hub"){
          steps{
                 withCredentials([string(credentialsId: 'dockerhubpassword', variable: 'dockerhubpassword')]) {
               // some block
                 sh 'docker login -u haseebsultan -p ${dockerhubpassword}'
                 sh 'docker image push haseebsultan/$JOB_NAME:v2.$BUILD_ID'
                 sh 'docker image push haseebsultan/$JOB_NAME:latest'
                 sh 'docker image rmi $JOB_NAME:v2.$BUILD_ID haseebsultan/$JOB_NAME:v2.$BUILD_ID haseebsultan/$JOB_NAME:latest'
}
               }

            }

       stage("Deployment of container"){
          steps{
               
                sshagent(['dockerhostpassword']) {
                 // some block
                sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.60.124 ${dockerrm}"
                sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.60.124 ${dockerimagerm}"
                sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.60.124 ${dockerrun}"
               }
          }

            }
}
}
