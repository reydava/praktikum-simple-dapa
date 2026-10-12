pipeline {
    agent any
    environment {
        IMAGE_NAME = 'pradityareydava/simple-app' 
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
        DOCKER_BIN = '"C:\\Users\\HP\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe"'
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
                    bat "${DOCKER_BIN} tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest"
                    echo "Image berhasil dibuat: ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Push image ke Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Login menggunakan file teks sementara agar aman di Windows bat
                        bat "echo %DOCKER_PASS% > token.txt"
                        bat "type token.txt | ${DOCKER_BIN} login -u %DOCKER_USER% --password-stdin"
                        bat "del token.txt"
                        
                        // Push image dan latest tag
                        bat "${DOCKER_BIN} push ${IMAGE_NAME}:${env.BUILD_NUMBER}"
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