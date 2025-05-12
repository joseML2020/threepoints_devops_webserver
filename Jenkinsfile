pipeline {
    agent any

    tools {
        // Nombre configurado en "Global Tool Configuration" para SonarQube Scanner
        sonarScanner 'SonarScanner'
    }

    options {
        // Limita ejecuciones simultáneas y mantiene últimas 10
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        IMAGE_NAME         = "devops_ws"
        // Clave y nombre de proyecto en SonarQube
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
                    sh """
                      sonar-scanner \
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        -Dsonar.projectName=\"${SONAR_PROJECT_NAME}\" \
                        -Dsonar.sources=src
                    """
                }
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh "docker build -t ${env.IMAGE_NAME} ."
                }
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
