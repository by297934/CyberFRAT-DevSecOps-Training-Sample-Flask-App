pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    saftey-key = "saftey"
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
          echo "Running TruffleHog secret scan..."
          sh "sh pipx install safety"
          sh "rm -rf saftey.json || true"
          sh "saftey scan requirement.txt --key $saftey-key"
          archiveArtifacts artifacts: 'trufflehog-report.json', allowEmptyArchive: true
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
