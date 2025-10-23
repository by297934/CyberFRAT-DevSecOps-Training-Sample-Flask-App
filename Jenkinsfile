pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
  }
  agent any

  stages {
    stage('Check for Secrets') {
      steps {
        script {
          echo "Running TruffleHog secret scan..."

          sh docker run trufflesecurity/trufflehog:latest --json https://github.com/by297934/CyberFRAT-DevSecOps-Training-Sample-Flask-App > trufflehog-report.json || true

          archiveArtifacts artifacts: 'trufflehog-report.json', allowEmptyArchive: true

          sh '''
            if grep -q '"DetectorType"' trufflehog-report.json; then
              echo "Secrets detected! Failing pipeline."
              exit 1
            else
              echo "No secrets found."
            fi
          '''
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
