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
            usernamePassword(credentialsId: 'acz', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY')
          ]) {
            sh 'packer build -var aws_access_key=${AWS_KEY} -var aws_secret_key=${AWS_SECRET} packer/packer.json'
        }
      }
    }
    stage('AWS Deployment') {
      steps {
          withCredentials([
            usernamePassword(credentialsId: 'acz', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY'),
            usernamePassword(credentialsId: 'GitHub_commands', passwordVariable: 'REPO_PASS', usernameVariable: 'REPO_USER'),
          ]) {
            sh 'rm -rf node-app-terraform'
            sh 'git clone https://github.com/cortesa/node-app-terraform.git'
            sh '''
               cd node-app-terraform
               terraform init
               terraform apply -auto-approve -var access_key=${AWS_KEY} -var secret_key=${AWS_SECRET}
               git add terraform.tfstate
               git -c user.name="cortesa" -c user.email="cortesa@gmail.com" commit -m "terraform state update from Jenkins"
               git push https://${REPO_USER}:${REPO_PASS}@ghttps://github.com/cortesa/node-app-terraform.git master
            '''
        }
      }
    }
  }
  environment {
    npm_config_cache = 'npm-cache'
  }
}