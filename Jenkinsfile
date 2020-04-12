pipeline {
  agent any
  stages {
    stage('Cloning') {
      steps {
        sh 'git clone -b k8s-v1.16 https://github.com/ankitjain28may/sqs-autoscaler-controller.git'
        stash includes: 'sqs-autoscaler-controller/', name: 'sqs-autoscaler-controller'
      }
    }
    stage('Build') {
      agent {
        docker {
          image 'golang:1.14.0-alpine3.11'
        }

      }
      steps {
        unstash 'sqs-autoscaler-controller'
        dir('sqs-autoscaler-controller') {
          sh 'pwd'
          sh 'ls'
          sh 'go mod download'
          sh 'CGO_ENABLED=0 go build -o sqs-autoscaler-controller main.go'
        }
        stash includes: 'sqs-autoscaler-controller/', name: 'sqs-autoscaler-controller'
      }
    }

    stage('Test') {
      steps {
        unstash 'sqs-autoscaler-controller'
        dir('sqs-autoscaler-controller') {
          sh 'pwd'
          sh './sqs-autoscaler-controller --help'
        }
        stash includes: 'sqs-autoscaler-controller/', name: 'sqs-autoscaler-controller'
      }
    }

    stage('Build Image') {
      steps {
        unstash 'sqs-autoscaler-controller'
        dir('sqs-autoscaler-controller') {
          sh 'pwd'
          sh '''
docker build -t sqs-autoscaler-controller .
docker build --target base -t sqs-autoscaler-controller:base .
docker images
'''
        }
        stash includes: 'sqs-autoscaler-controller/', name: 'sqs-autoscaler-controller'
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