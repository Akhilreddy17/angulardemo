pipeline{
  agent any
  stages{
    stage ('checkout'){
      steps{
        sh 'git checkout master'
      }
    }
   stage ('Build'){
       parallel {
             stage("Angular Build") {
                  agent {
                      docker { image 'node:10' }
                  }
                  steps {
                         sh '''
                               echo Installing packages
                               npm install
                               npm install -g @angular/cli@8
                               echo Building Angular Project
                               ng build
                         '''
                  }
             }
              stage("S3 Build") {
                  steps { 
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]){ 
                         //aws cloudformation create-stack --stack-name S3bucketcreation --template-body file:cft.yaml
                         sh 'aws s3api creat-bucket --bucket busireddy --region us-east-1'
                    }
                  }
              }
       }
   }
    stage ('Deploy'){
      steps{
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'key', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]){
       
        sh 'aws s3 ls'
        sh 'aws s3 sync . s3://busireddy/ --region us-east-1'
        }
      } 
       }
   }
    
}

