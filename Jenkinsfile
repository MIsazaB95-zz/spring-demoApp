pipeline {
  agent any
  environment {
    ARTIFACT_ID = "486912667928.dkr.ecr.us-east-1.amazonaws.com/spring-app:${env.BUILD_NUMBER}"
  }
  options {
    timeout(time: 5, unit: 'MINUTES')
  }
  stages {
    stage('Clean') {
      steps {
        sh 'mvn clean'
      }
    }
    stage('Validate') {
      steps {
        sh 'mvn validate'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }
    stage('Package') {
      steps {
        sh 'mvn package'
      }
    }
    stage('Build docker') {
      steps {
        script {
          dockerImage = docker.build "${env.ARTIFACT_ID}"
        }
      }
    }
    stage('Publish') {
      steps {
        script {
          docker.withRegistry("https://486912667928.dkr.ecr.us-east-1.amazonaws.com","ecr:us-east-1:AWSCLI"){
            dockerImage.push()
          }
        }
      }
    }
  }
}