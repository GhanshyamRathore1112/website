pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:202051072'
       appRegistry = "822540995489.dkr.ecr.ap-northeast-1.amazonaws.com/capstone"
       capstoneRegistry = "https://822540995489.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "capstone"
        service = "capstone"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/GhanshyamRathore1112/website'
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
            withAWS(credentials: '202051072', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
