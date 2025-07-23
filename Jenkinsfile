pipeline {
  agent any

  environment {
    IMAGE_NAME = 'devops-showcase-app'
    IMAGE_TAG = 'latest'
    DOCKERFILE_PATH = 'docker/Dockerfile'
    SONARQUBE_ENV = 'SonarQube'
    SONAR_CREDENTIAL_ID = 'sonar-token'
    SONAR_PROJECT_KEY = 'userservice'
    APP_PATH = 'springboot-app'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        dir("${APP_PATH}") {
          sh 'chmod +x mvnw'
          sh './mvnw clean package -DskipTests'
        }
      }
    }

    stage('Test') {
      steps {
        dir("${APP_PATH}") {
          sh './mvnw test'
        }
      }
    }

    stage('Docker Build') {
      steps {
        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f ${DOCKERFILE_PATH} ."
      }
    }

    stage('SonarQube Analysis') {
      steps {
        dir("${APP_PATH}") {
          withSonarQubeEnv("${SONARQUBE_ENV}") {
            withCredentials([string(credentialsId: "${SONAR_CREDENTIAL_ID}", variable: 'SONAR_TOKEN')]) {
            sh "./mvnw clean verify sonar:sonar -Dsonar.projectKey=$SONAR_PROJECT_KEY -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.token=$SONAR_TOKEN"
            }
          }
        }
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([
          usernamePassword(
            credentialsId: 'jfrog-creds-id',
            usernameVariable: 'USERNAME',
            passwordVariable: 'PASSWORD'
          )
        ]) {
          sh "docker login -u $USERNAME -p $PASSWORD trial30upoh.jfrog.io"
          sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} trial30upoh.jfrog.io/docker-trial/${IMAGE_NAME}:${IMAGE_TAG}"
          sh "docker push trial30upoh.jfrog.io/docker-trial/${IMAGE_NAME}:${IMAGE_TAG}"
        }
      }
    }
  }
}
