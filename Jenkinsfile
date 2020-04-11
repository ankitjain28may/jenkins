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
            sh '''echo "Cloning"
git clone -b k8s-v1.16 https://github.com/ankitjain28may/sqs-autoscaler-controller.git
cd sqs-autoscaler-controller
echo "######Dockerfile#######"
cat Dockerfile

sed "s/FROM scratch/ADD https:\\/\\/get.aquasec.com\\/microscanner .\\\\\\nRUN chmod +x microscanner\\\\\\nRUN .\\/microscanner ZTc0NTkyMTVkY2Ux\\\\\\nFROM scratch/" ./Dockerfile

echo "######Dockerfile#######"
cat Dockerfile

docker build -t sqs-autoscaler-controller .

docker images

docker run -d sqs-autoscaler-controller:latest "--help"'''
            sh '''rm -rf sqs-autoscaler-controller
'''
          }
        }

      }
    }

  }
}