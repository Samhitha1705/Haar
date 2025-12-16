pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "vedasamhitha17/backend:latest"
        FRONTEND_IMAGE = "vedasamhitha17/frontend:latest"
        BACKEND_PORT = "8080"
        FRONTEND_PORT = "3000"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/Samhitha1705/Haar.git', branch: 'main', credentialsId: '12009f48-c4c7-4043-a81e-480e3e90c789'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-password', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    dir('backend') {
                        echo "Building backend Docker image..."
                        bat "docker build -t ${BACKEND_IMAGE} ."
                    }
                    dir('frontend') {
                        echo "Building frontend Docker image..."
                        bat "docker build -t ${FRONTEND_IMAGE} ."
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    echo "Pushing backend image..."
                    bat "docker push ${BACKEND_IMAGE}"
                    echo "Pushing frontend image..."
                    bat "docker push ${FRONTEND_IMAGE}"
                }
            }
        }

        stage('Clean Existing Containers & Free Ports') {
            steps {
                script {
                    echo "Stopping and removing existing containers if running..."
                    bat "docker rm -f backend frontend 2>nul || echo Containers not running"

                    echo "Freeing ports ${BACKEND_PORT} and ${FRONTEND_PORT}..."
                    bat """
                    FOR /F "tokens=5" %%a IN ('netstat -aon ^| findstr :${BACKEND_PORT}') DO taskkill /F /PID %%a >nul 2>&1
                    FOR /F "tokens=5" %%a IN ('netstat -aon ^| findstr :${FRONTEND_PORT}') DO taskkill /F /PID %%a >nul 2>&1
                    """
                }
            }
        }

        stage('Deploy Containers') {
            steps {
                script {
                    echo "Deploying backend..."
                    bat "docker run -d -p ${BACKEND_PORT}:8080 --name backend ${BACKEND_IMAGE}"

                    echo "Deploying frontend..."
                    bat "docker run -d -p ${FRONTEND_PORT}:80 --name frontend ${FRONTEND_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            node {
                echo "Cleaning frontend node_modules safely..."
                dir('frontend') {
                    bat 'if exist node_modules rmdir /s /q node_modules || echo node_modules already cleaned'
                }
            }
        }
    }
}
