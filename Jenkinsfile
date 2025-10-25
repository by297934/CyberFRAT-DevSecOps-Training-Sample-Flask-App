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
    stage('Check for Secrets') {
      steps {
        withCredentials([string(credentialsId: 'safety', variable: 'SAFETY_KEY')]) {
          sh '''
          pipx install safety
          /var/lib/jenkins/.local/bin/safety --key "$SAFETY_KEY" scan "$WORKSPACE" > safety.txt
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
