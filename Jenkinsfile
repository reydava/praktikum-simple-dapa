pipeline {
    agent any
    environment {
        IMAGE_NAME = 'reydava/simple-app' 
        REGISTRY = 'https://index.docker.io/v1/'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
        // Menentukan path absolut Docker agar terbaca di Windows Service Jenkins
        DOCKER_BIN = '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe"'
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Melakukan checkout dari SCM...'
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo 'Mulai build aplikasi'
                bat 'echo Build selesai'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Membangun Docker image...'
                    bat "${DOCKER_BIN} build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} ."
                    echo "Image berhasil dibuat: ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Push image ke Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat "echo %DOCKER_PASS% | ${DOCKER_BIN} login -u %DOCKER_USER% --password-stdin"
                        bat "${DOCKER_BIN} push ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                        bat "${DOCKER_BIN} tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest"
                        bat "${DOCKER_BIN} push ${IMAGE_NAME}:latest"
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Selesai build 🏁'
        }
    }
}