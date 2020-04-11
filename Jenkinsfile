pipeline {
  agent {
    docker {
      image 'node:latest'
      args '''-p 3000:3000
-u root:root'''
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
      parallel {
        stage('Deliver') {
          steps {
            sh '''tee kill.sh <<EOF
#!/bin/bash
set -euo pipefail
pwd
rm -rf ../jenkins_multi-branch-1/scraping-nodejs
EOF

echo "Running kill.sh"

chmod +x kill.sh
./kill.sh
'''
            input '(Click "Proceed" to continue)'
          }
        }

        stage('Prod') {
          steps {
            sh '''tee kill.sh <<EOF
#!/bin/bash
set -euo pipefail

rm -rf ../jenkins_multi-branch-1/sqs-autoscaler-controller
docker images

EOF

chmod +x kill.sh
./kill.sh'''
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