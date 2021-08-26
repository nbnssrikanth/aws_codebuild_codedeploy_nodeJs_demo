pipeline {
    agent any
    environment {
        registry = "559247606700.dkr.ecr.us-east-1.amazonaws.com/dockerpipeline"
        image = 'dockerpipeline'
    }
   
    stages {
        stage('Cloning Git') {
            steps {
              git 'https://github.com/nbnssrikanth/aws_codebuild_codedeploy_nodeJs_demo'
            }
        }
 

// Building Docker images
    stage('Building the image of dockers') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 559247606700.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 559247606700.dkr.ecr.us-east-1.amazonaws.com/dockerpipeline:latest'
         }
        }
      }
        stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=myreactjs -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=myreactjs -q | xargs -r docker container rm'
         }
       }

      
    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 3000:3000 --rm --name myreactjs 559247606700.dkr.ecr.us-east-1.amazonaws.com/dockerpipeline'
            }
      }
    }
    }


  post
  {
    always
    {
    sh 'docker rmi $(docker images --filter "dangling=true" -q --no-trunc) 2>/dev/null'
    }
 }

}

