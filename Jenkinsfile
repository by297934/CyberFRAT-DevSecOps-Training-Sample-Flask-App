pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
  }
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
     
    stage('Snyk Scan') {
      steps {
        sh '''
        . venv/bin/activate
        pip install -r requirements.txt
        '''
        snykSecurity(
          snykInstallation: 'Default',
          snykTokenId: 'snyk',
          failOnIssues: false,
          targetFile: 'requirements.txt'
          )
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        script {
          docker.withRegistry('', registryCredential) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Test Run') {
      steps {
        sh "docker run -d ${registry}:${BUILD_NUMBER}"
      }
    }
  }
}
