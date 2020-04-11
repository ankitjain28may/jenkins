pipeline {
  agent any
  stages {
    stage('Build') {
      agent {
        docker {
          image 'node:latest'
          args '-p 3000:3000'
        }

      }
      steps {
        sh 'npm --version'
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
      parallel {
        stage('Deliver') {
          steps {
            sh 'echo "Deliver"'
            input '(Click "Proceed" to continue)'
          }
        }

        stage('Prod') {
          steps {
            sh '''tee deliver.sh <<EOF
#!/bin/bash
set -euo pipefail

echo "Cloning"
git clone -b k8s-v1.16 https://github.com/ankitjain28may/sqs-autoscaler-controller.git
cd sqs-autoscaler-controller
docker build -t sqs-autoscaler-controller .
docker images

EOF


chmod +x deliver.sh
./deliver.sh'''
            sh '''tee kill.sh <<EOF
#!/bin/bash
set -euo pipefail

rm -rf sqs-autoscaler-controller
docker images

EOF

chmod +x kill.sh
./kill.sh'''
          }
        }

      }
    }

  }
}