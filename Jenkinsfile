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
            sh '''
apt-get update -y &&  apt-get install tree -y
tree -L 4
tee deliver.sh <<EOF
#!/bin/bash
set -euo pipefail

echo "Cloning"
git clone https://github.com/ankitjain28may/scraping-nodejs.git
cd scraping-nodejs
npm install
tree -L 4

EOF

echo "Running deliver.sh"

chmod +x deliver.sh
./deliver.sh

tee kill.sh <<EOF
#!/bin/bash
set -euo pipefail
pwd
rm -rf scraping-nodejs
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