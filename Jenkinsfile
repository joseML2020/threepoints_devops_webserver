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
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarDocker') {
          // Ejecutar el scanner desde un contenedor Docker oficial
          script {
            docker.image('sonarsource/sonar-scanner-cli:latest').inside {
              sh "sonar-scanner \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.projectName=\"${SONAR_PROJECT_NAME}\" \
                -Dsonar.sources=src"
            }
          }
        }
        timeout(time: 5, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: false
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
