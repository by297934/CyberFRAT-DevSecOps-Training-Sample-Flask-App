pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    safety_key = credentials('safety')
  }
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('scan') {
          steps { 
            script { 
              sh'''
              pipx install safety 
              /var/lib/jenkins/.local/bin/safety --key "${safety_key}" scan "${WORKSPACE}" > safety.txt || true
              '''
              archiveArtifacts artifacts: 'safety.txt', allowEmptyArchive: true 
            } 
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
