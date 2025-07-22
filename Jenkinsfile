pipeline {
  agent any

  environment {
    IMAGE_NAME = 'devops-showcase-app'
    IMAGE_TAG = 'latest'
    DOCKERFILE_PATH = 'docker/Dockerfile'
    SONARQUBE_ENV = 'SonarQube'
    SONAR_CREDENTIAL_ID = 'sonar-token'
    APP_PATH = 'springboot-app'
  }

  stages {
    stage('Build') {
      steps {
        sh "./${APP_PATH}/mvnw clean package -DskipTests"
      }
    }

    stage('Test') {
      steps {
        sh "./${APP_PATH}/mvnw test"
      }
    }

    stage('Docker Build') {
      steps {
        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f ${DOCKERFILE_PATH} ."
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv("${SONARQUBE_ENV}") {
          withCredentials([string(credentialsId: "${SONAR_CREDENTIAL_ID}", variable: 'SONAR_TOKEN')]) {
            sh "./${APP_PATH}/mvnw verify sonar:sonar -Dsonar.token=$SONAR_TOKEN"
          }
        }
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword
        (credentialsId: 'jfrog-creds-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')
        ]) {
          sh "docker login -u $USERNAME -p $PASSWORD trial30upoh.jfrog.io"
          sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} trial30upoh.jfrog.io/docker-trial/${IMAGE_NAME}:${IMAGE_TAG}"
          sh "docker push trial30upoh.jfrog.io/docker-trial/${IMAGE_NAME}:${IMAGE_TAG}"
        }
      }
    }
  }
}
