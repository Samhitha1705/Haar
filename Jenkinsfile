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
                    
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
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
                script {
                    // Stop/remove existing containers safely
                    bat """
                    docker rm -f backend frontend 2>nul || echo Containers not running
                    """

                    // Run backend container
                    bat "docker run -d -p ${BACKEND_PORT}:8080 --name backend ${BACKEND_IMAGE}"

                    // Run frontend container
                    bat "docker run -d -p ${FRONTEND_PORT}:80 --name frontend ${FRONTEND_IMAGE}"
                }
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
