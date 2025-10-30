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
          curl https://static.snyk.io/cli/latest/snyk-linux -o snyk
          chmod +x ./snyk
          . myvenv/bin/activate
          ./snyk auth "$snyk_key"
          ./snyk monitor --all-projects --org=e7680b53-402f-451f-b9c1-f3d10131b14d || true
          '''
        }
      }
    }
    
    stage ('SAST SCan') {
      steps {
        script {
          sh '''
          docker pull semgrep/semgrep
          docker run -e SEMGREP_APP_TOKEN=ed21e970f88e755aa143c273bd72d085e7b8a3a5bf4ea07c0e1adaa75081867d --rm -v "${WORKSPACE}:/src" semgrep/semgrep semgrep ci
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
