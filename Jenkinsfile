pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:jenkins-id'
       appRegistry = "capstoneproject"
       capstoneRegistry = "https://950332842981.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "capstone-cluster"
        service = "capstoneService"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Krishna841/website'
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
            withAWS(credentials: 'jenkins-id', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
