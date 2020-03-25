pipeline {
  agent any
  environment {
    ARTIFACT_ID = "miguelisaza95/spring-demo:${env.BUILD_NUMBER}"
  }
  options {
    timeout(time: 2, unit: 'MINUTES')
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }
    stage('Deliver') {
      steps {
        sh '''chmod +x ./scripts/deliver.sh
./scripts/deliver.sh'''
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
          docker.withRegistry("","dockerhub"){
            dockerImage.push()
          }
        }

      }
    }
  }
}