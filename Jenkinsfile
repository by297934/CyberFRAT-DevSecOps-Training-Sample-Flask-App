pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    saftey_key = credentials('safety')
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
        script {
          sh "pipx install safety"
          sh '/var/lib/jenkins/.local/bin/safety check --key "$SAFETY_KEY" -r "${WORKSPACE}/requirements.txt" --json > safety.json'         
          archiveArtifacts artifacts: 'saftey.json', allowEmptyArchive: true
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
