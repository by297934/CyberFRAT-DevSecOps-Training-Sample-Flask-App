pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    SAFETY_KEY = credentials('safety')
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
              sh 'pipx install safety' 
              sh '/var/lib/jenkins/.local/bin/safety --key e8836398-a8ae-47b7-9cd3-ce2533e4ca3b scan "${WORKSPACE}" > safety.txt' 
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
