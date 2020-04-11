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
      steps {
        sh '''
apk update -y &&  apk add tree -y
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

  }
}