pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    snyk_key = credentials('snyk2')
    semgrep_key = credentials('semgrep')
  }
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    
    stage ('SAST SCan') {
      steps {
        script {
          sh '''
          docker pull semgrep/semgrep
          docker run -e SEMGREP_APP_TOKEN=${semgrep_key} --rm -v "${WORKSPACE}:/src" semgrep/semgrep semgrep ci --code
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
