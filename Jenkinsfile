pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "vedasamhitha17/backend:latest"
        FRONTEND_IMAGE = "vedasamhitha17/frontend:latest"
        BACKEND_PORT = "8081"   // changed to avoid conflict
        FRONTEND_PORT = "3000"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/Samhitha1705/Haar.git',
                    branch: 'main',
                    credentialsId: '12009f48-c4c7-4043-a81e-480e3e90c789'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-password',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {

                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                dir('backend') {
                    bat "docker build -t ${BACKEND_IMAGE} ."
                }
                dir('frontend') {
                    bat "docker build -t ${FRONTEND_IMAGE} ."
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                bat "docker push ${BACKEND_IMAGE}"
                bat "docker push ${FRONTEND_IMAGE}"
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                docker rm -f backend frontend 2>nul || echo Containers not running
                docker run -d -p 8081:8080 --name backend vedasamhitha17/backend:latest
                docker run -d -p 3000:80 --name frontend vedasamhitha17/frontend:latest
                '''
            }
        }
    }

    post {
        always {
            echo "Cleaning frontend node_modules safely"
            dir('frontend') {
                bat 'if exist node_modules rmdir /s /q node_modules || echo node_modules already cleaned'
            }
        }
    }
}
