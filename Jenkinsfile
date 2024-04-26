pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node 16'
	sonarqubeScanner 'SonarQube-Scanner' // Ajout de l'outil SonarScanner
    }
    environment {
        SCANNER_HOME = tool 'SonarQube-Scanner' // Définition de SCANNER_HOME sans l'option -X
        APP_NAME = "reddit-clone-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "bsidibe2024"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/bsidibe91/a-reddit-clone.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Reddit-Clone-CI \
                    -Dsonar.projectKey=Reddit-Clone-CI'''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-Token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
             }

	}
    }
}
