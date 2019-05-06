pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    label "jenkins-maven"
  }
  environment {
    DEPLOY_NAMESPACE = "web-production"
    registryCredential = "Harbor"
  }
  stages {
    stage('Build and Push Docker Images') {
      steps {
        container('maven') {
          dir('web/docker') {
            script {
              docker.withRegistry('http://harbor.corp.local', registryCredential) {
                def dockerfile = 'dockerfile-app1'
                def customImage = docker.build("corp-web/app1:v1", "-f ${dockerfile} ./")
                customImage.push()
              }
            }
          } 
        }
      }
    }
    stage('Validate Environment') {
      steps {
        container('maven') {
          dir('web') {
            sh 'jx step helm build'
          }
        }
      }
    }
    stage('Update Environment') {
      when {
        branch 'master'
      }
      steps {
        container('maven') {
          dir('web'){
            sh 'jx step helm apply'
          }
        }
      }
    }
  }
}
