pipeline {
    agent any
    environment {
        IMAGE_NAME = 'pradityareydava/simple-app' 
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
        DOCKER_BIN = 'C:\\Users\\HP\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
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
        stage('Database Connection Check') {
            steps {
                echo 'Memeriksa koneksi ke database (Simulasi/Koneksi aktif)...'
                bat 'echo Koneksi ke database berhasil terhubung.'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Membangun Docker image...'
                    powershell """
                        & "${env.DOCKER_BIN}" build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} .
                        & "${env.DOCKER_BIN}" tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest
                    """
                    echo "Image berhasil dibuat: ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Push image ke Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        powershell """
                            \$password = \$env:DOCKER_PASS | ConvertTo-SecureString -AsPlainText -Force
                            \$creds = New-Object System.Management.Automation.PSCredential(\$env:DOCKER_USER, \$password)
                            & "${env.DOCKER_BIN}" login -u \$creds.UserName -p \$env:DOCKER_PASS
                            
                            & "${env.DOCKER_BIN}" push ${IMAGE_NAME}:${env.BUILD_NUMBER}
                            & "${env.DOCKER_BIN}" push ${IMAGE_NAME}:latest
                        """
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