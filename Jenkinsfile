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

    stage('Deliver') {
      steps {
        sh '''tee deliver.sh <<EOF
#!/bin/bash
set -euo pipefail

echo "Cloning"
git clone https://github.com/ankitjain28may/scraping-nodejs.git
cd scraping-nodejs
npm install
tree -L 4


EOF

chmod +x deliver.sh
./deliver.sh'''
        input '(Click "Proceed" to continue)'
      }
    }

  }
}