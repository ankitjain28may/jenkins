pipeline {
  agent {
    docker {
      image 'node:latest'
      args '-p 3000:3000'
    }

  }
  stages {
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }

    stage('Test') {
      environment {
        CI = 'testing'
      }
      steps {
        sh 'echo "Testing"'
      }
    }

  }
}