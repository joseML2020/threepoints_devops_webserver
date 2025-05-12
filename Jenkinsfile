pipeline {
  agent any

  options {
    disableConcurrentBuilds()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  environment {
    IMAGE_NAME         = "devops_ws"
    SONAR_PROJECT_KEY  = "threepoints_devops_webserver"
    SONAR_PROJECT_NAME = "ThreePoints Webserver"
    SONAR_HOST_URL     = "http://host.docker.internal:9000"
    SONAR_AUTH_TOKEN   = credentials('sonar-token')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SonarQube Analysis') {
      steps {
        script {
          docker.image('sonarsource/sonar-scanner-cli:latest').inside {
            sh """
              sonar-scanner \
                -Dsonar.host.url=${SONAR_HOST_URL} \
                -Dsonar.login=${SONAR_AUTH_TOKEN} \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.projectName=\"${SONAR_PROJECT_NAME}\" \
                -Dsonar.sources=src
            """
          }
        }
      }
    }

    stage('Build') {
      steps {
        sh "docker build -t ${IMAGE_NAME} ."
      }
    }
  }

  post {
    success {
      echo "Pipeline completado con éxito."
    }
    failure {
      echo "Ha fallado alguna etapa del pipeline."
    }
  }
}
