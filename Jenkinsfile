pipeline {
  environment {
    registry = "bharat1200/testrep"
    registryCredential = "dockcred"
    dockerImage = ''
  }
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('setting up enviorment') {
      steps {
        script {
          sh '''
          python3 -m venv myvenv
          . myvenv/bin/activate
          ls
          pip install -r requirement.txt
          '''
        }
      }
    }    
    stage('Snyk Scan') {
      steps {
        sh '.venv/bin/activate'
        snykSecurity(
          snykInstallation: 'Default',   
          snykTokenId: 'snyk',
          failOnIssues: false,
          monitorProjectOnBuild: true,
          organisation: 'test',
          projectName: 'MyJenkinsProject',
          )
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
