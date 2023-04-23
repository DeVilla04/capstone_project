pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-2:aws_cred'
       appRegistry = "844869865208.dkr.ecr.us-east-2.amazonaws.com/capstone_project"
       capstoneRegistry = "https://844869865208.dkr.ecr.us-east-2.amazonaws.com/"
       cluster = "capstone_project"
        service = "capstone_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/DeVilla04/capstone_project'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'aws_cred', region: 'us-east-2'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
