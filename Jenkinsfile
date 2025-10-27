pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
    snyk_key = credentials('snyk2')
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
        script {
          sh '''
          . myvenv/bin/activate
          snyk auth "$snyk_key"
          snyk test --org=e7680b53-402f-451f-b9c1-f3d10131b14d
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
