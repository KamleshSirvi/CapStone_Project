pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:kamleshkumar'
       appRegistry = "550499372643.dkr.ecr.ap-south-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://550499372643.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "capStoneProject"
        service = "CapStone"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/KamleshSirvi/CapStone_Project.git'
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
            withAWS(credentials: 'kamleshKumarNewCredentials', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
