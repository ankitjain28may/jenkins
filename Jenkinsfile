pipeline {
  agent any
  stages {
    stage('Cloning') {
      steps {
        sh 'git clone -b k8s-v1.16 https://github.com/ankitjain28may/sqs-autoscaler-controller.git'
      }
    }
    stage('Build') {
      agent {
        docker {
          image 'golang:1.14.0-alpine3.11'
        }

      }
      steps {
        dir('sqs-autoscaler-controller') {
          sh 'pwd'
          sh '''
pwd
ls
go mod download
CGO_ENABLED=0 go build -o sqs-autoscaler-controller main.go
'''
        }
      }
    }

    stage('Test') {
      steps {
        dir('sqs-autoscaler-controller') {
          sh 'pwd'
          sh './sqs-autoscaler-controller --help'
        }
      }
    }

    stage('Build Image') {
      steps {
        dir('sqs-autoscaler-controller') {
          sh 'pwd'
          sh '''
docker build -t sqs-autoscaler-controller .
docker build --target base -t sqs-autoscaler-controller:base .
docker images
'''
        }
      }
    }

    stage('Aqua Scanner') {
      steps {
        aquaMicroscanner(imageName: 'sqs-autoscaler-controller:base', outputFormat: 'json', notCompliesCmd: 'exit 1', onDisallowed: 'fail')
      }
    }

    stage('Running container') {
      steps {
        sh 'docker run --rm sqs-autoscaler-controller:latest "--help"'
      }
    }

    stage('Clean Up') {
      steps {
        sh 'rm -rf sqs-autoscaler-controller'
        sh 'docker ps -a'
      }
    }
  }
}