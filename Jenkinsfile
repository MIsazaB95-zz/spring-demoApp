pipeline {
  agent any
  environment {
    ARTIFACT_ID = "486912667928.dkr.ecr.us-east-1.amazonaws.com/spring-app:latest"
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
    stage('Deploy') {
      steps {
        withCredentials([[$class:'AmazonWebServicesCredentialsBinding', accessKeyVariable:'AWS_ACCESS_KEY_ID',
        credentialsId: "AWSCLI", secretKeyVariable:'AWS_SECRET_ACCESS_KEY']]){
          sh '''
          aws eks --region us-east-1 update-kubeconfig --name eks_cluster_tuto
          helm install rampup ./spring-demo
          kubectl get svc,po,deploy
          '''
        }
      }
    }
  }
  post {
    success {
        withCredentials([[$class:'AmazonWebServicesCredentialsBinding', accessKeyVariable:'AWS_ACCESS_KEY_ID',
        credentialsId: "AWSCLI", secretKeyVariable:'AWS_SECRET_ACCESS_KEY']]){
        sh 'kubectl get svc spring-app -o jsonpath="{.status.loadBalancer.ingerss[*].hostname}"; echo'
      }
    }
  }
}