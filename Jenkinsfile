pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh './springboot-app/mvnw clean package'
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t devops-showcase-app -f docker/Dockerfile .'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
            sh './springboot-app/mvnw verify sonar:sonar -Dsonar.token=$SONAR_TOKEN'
          }
        }
      }
    }
  }
}
