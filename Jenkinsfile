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
          echo "🔍 Running TruffleHog secret scan..."

      // Run TruffleHog and save output to a JSON report file
        sh '''
        docker run --rm \
          -v $(pwd):/repo \
          trufflesecurity/trufflehog \
          filesystem /repo --json > trufflehog-report.json || true
      '''

      // Archive the report in Jenkins for review
      archiveArtifacts artifacts: 'trufflehog-report.json', allowEmptyArchive: true

      // Fail the build if secrets are found
      sh '''
        if grep -q '"DetectorType"' trufflehog-report.json; then
          echo "❌ Secrets detected! Failing pipeline."
          exit 1
        else
          echo "✅ No secrets found."
        fi
      '''
    }
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
