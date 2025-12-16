pipeline {
    agent any

    environment {
        BACKEND_IMAGE  = "vedasamhitha17/backend"
        FRONTEND_IMAGE = "vedasamhitha17/frontend"
        TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Samhitha1705/Haar.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-pass', variable: 'DOCKER_PASS')]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u vedasamhitha17 --password-stdin
                    '''
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
                    // Clean node_modules safely
                    bat 'rmdir /s /q node_modules 2>nul || exit 0'
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat '''
                docker build -t %BACKEND_IMAGE%:%TAG% backend
                docker build -t %FRONTEND_IMAGE%:%TAG% frontend
                '''
            }
        }

        stage('Docker Push') {
            steps {
                bat '''
                docker push %BACKEND_IMAGE%:%TAG%
                docker push %FRONTEND_IMAGE%:%TAG%
                '''
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                docker rm -f backend frontend 2>nul || echo Containers not running
                docker run -d -p 8080:8080 --name backend %BACKEND_IMAGE%:%TAG%
                docker run -d -p 3000:80 --name frontend %FRONTEND_IMAGE%:%TAG%
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Jenkins Full Stack Pipeline Succeeded'
        }
        failure {
            echo '❌ Jenkins Pipeline Failed'
        }
        always {
            echo 'Cleaning frontend node_modules safely'
            dir('frontend') {
                // Use returnStatus so errors do NOT fail the pipeline
                bat(script: 'rmdir /s /q node_modules 2>nul', returnStatus: true)
            }
        }
    }
}
