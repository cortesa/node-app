pipeline {
  agent {
    docker {
      image 'goforgold/build-container:latest'
    }

  }
  stages {
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }
    stage('Create Packer AMI') {
        steps {
          withCredentials([
            $class: 'AmazonWebServicesCredentialsBinding'
            credentialsId: 'ACZ',
            secretKeyVariable: 'AWS_SECRET',
            accessKeyVariable: 'AWS_KEY'
          ]) {
            sh 'packer build -var aws_access_key=${AWS_KEY} -var aws_secret_key=${AWS_SECRET} packer/packer.json'
        }
      }
    }
  }
  environment {
    npm_config_cache = 'npm-cache'
  }
}