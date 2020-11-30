pipeline{
    agent any
    stages{
       stage("Pull Code From Github"){
          steps{
                git 'https://github.com/haseebsultan/dotnet-docker-samples.git'
               }

            }
       stage("Build Docker File"){
          steps{
                sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID aspnetapp/.'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID haseebsultan/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID haseebsultan/$JOB_NAME:latest'
               }

            }
        stage("Push image to docker hub"){
          steps{
                 withCredentials([string(credentialsId: 'dockerhubpassword', variable: 'dockerhubpassword')]) {
               // some block
                 sh 'docker login -u haseebsultan -p ${dockerhubpassword}'
                 sh 'docker image push haseebsultan/$JOB_NAME:v1.$BUILD_ID'
                 sh 'docker image push haseebsultan/$JOB_NAME:latest'
                 sh 'docker image rmi $JOB_NAME:v1.$BUILD_ID haseebsultan/$JOB_NAME:v1.$BUILD_ID haseebsultan/$JOB_NAME:latest'
}
               }

            }

       stage("Deployment of container"){
          steps{
                def dockerrun = 'docker run -p 8000:80 -d --name sampledotnet haseebsultan/dotnet-app:latest'
                def dockerrm = 'docker container rm -f sampledotnet'
                def dockerimagerm = 'docker image rmi -f haseebsultan/dotnet-app'
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