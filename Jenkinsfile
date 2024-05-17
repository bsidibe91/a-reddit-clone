pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "timeguard-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "bsidibe91"
        DOCKER_PASS = 'dockerhub' // Cette ligne devrait contenir le mot de passe réel de Docker Hub ou une référence aux identifiants de Jenkins
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}" // Suppression de la concaténation inutile
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN") // Assurez-vous que cet ID d'authentification est correct
    }
    stages {
        stage('Nettoyer l'espace de travail') {
            steps {
                cleanWs()
            }
        }
        stage('Récupérer depuis Git') {
            steps {
                git branch: 'main', url: 'https://github.com/bsidibe91/appli.git'
            }
        }
        stage("Analyse SonarQube") {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh """${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectName=TimeGuard \
                    -Dsonar.projectKey=TimeGuard"
                }
            }
        }
        stage("Portail de Qualité") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-Token'
                }
            }
        }
        stage('Installer les dépendances') {
            steps {
                sh "npm install"
            }
        }
        stage('Analyse Trivy FS') { // Renommé pour suivre la convention
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    }
}
