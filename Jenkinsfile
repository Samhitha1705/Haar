pipeline {
    agent any

    environment {
        DOCKER_USER = 'vedasamhitha17'
        BACKEND_IMAGE = 'vedasamhitha17/backend:latest'
    }

    stages {

        stage('Checkout SCM') {
            steps {
                git branch: 'main',
                    credentialsId: '12009f48-c4c7-4043-a81e-480e3e90c789',
                    url: 'https://github.com/Samhitha1705/Haar.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_PASS')]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    bat '''
                    docker build -t %BACKEND_IMAGE% .
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat '''
                docker push %BACKEND_IMAGE%
                '''
            }
        }

        stage('Deploy Backend') {
            steps {
                bat '''
                echo Stopping old backend container if exists...
                docker ps -q --filter "name=backend" | findstr . && docker rm -f backend || echo backend not running

                echo Running new backend container on port 8081...
                docker run -d -p 8081:8080 --name backend %BACKEND_IMAGE%
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning frontend node_modules safely'
            dir('frontend') {
                bat '''
                if exist node_modules rmdir /s /q node_modules || echo node_modules already cleaned
                '''
            }
        }

        success {
            echo '✅ Pipeline executed successfully'
        }

        failure {
            echo '❌ Pipeline failed'
        }
    }
}
