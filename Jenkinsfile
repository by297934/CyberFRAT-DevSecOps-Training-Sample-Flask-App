pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    snyk_key = credentials('snyk')
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
        snykSecurity(
          snykInstallation: 'Default',           // matches name from Tools config
          )
        script {
          sh '''
          synk auth "${snyk}"
          synk code test
          '''
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
