pipeline {
    agent any

    environment {
        DOCKER_USER = 'vedasamhitha17'
        DOCKER_PASS = credentials('docker-hub-password') // Replace with your Jenkins credential ID
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASS')]) {
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    bat 'mvn clean package'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'rmdir /s /q node_modules 2>nul || echo node_modules not present'
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    // Build backend and frontend images
                    bat 'docker build -t vedasamhitha17/backend:latest ./backend'
                    bat 'docker build -t vedasamhitha17/frontend:latest ./frontend'

                    // Push images to Docker Hub
                    bat 'docker push vedasamhitha17/backend:latest'
                    bat 'docker push vedasamhitha17/frontend:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                REM Stop and remove existing containers
                docker rm -f backend frontend 2>nul || echo Containers not running

                REM Pull latest images to ensure availability
                docker pull vedasamhitha17/backend:latest
                docker pull vedasamhitha17/frontend:latest

                REM Run backend and frontend containers
                docker run -d -p 8080:8080 --name backend vedasamhitha17/backend:latest
                docker run -d -p 3001:80 --name frontend vedasamhitha17/frontend:latest
                '''
            }
        }
    }

    post {
        always {
            echo "Cleaning frontend node_modules safely"
            dir('frontend') {
                bat 'rmdir /s /q node_modules 2>nul || echo node_modules already cleaned'
            }
        }

        success {
            echo "✅ Jenkins Pipeline Completed Successfully"
        }

        failure {
            echo "❌ Jenkins Pipeline Failed"
        }
    }
}
