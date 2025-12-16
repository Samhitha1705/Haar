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
                    bat '''
                    echo ===== Configuring npm =====
                    npm config set fetch-retries 5
                    npm config set fetch-retry-mintimeout 20000
                    npm config set fetch-retry-maxtimeout 120000
                    npm config set cache "%WORKSPACE%\\.npm-cache" --global

                    echo ===== Installing dependencies =====
                    npm install

                    echo ===== Building frontend =====
                    npm run build
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat """
                docker build -t %BACKEND_IMAGE%:%TAG% backend
                docker build -t %FRONTEND_IMAGE%:%TAG% frontend
                """
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

        stage('Docker Push') {
            steps {
                bat """
                docker push %BACKEND_IMAGE%:%TAG%
                docker push %FRONTEND_IMAGE%:%TAG%
                """
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                docker rm -f backend frontend 2>nul || echo Containers not running
                docker run -d -p 8080:8080 --name backend %BACKEND_IMAGE%:%TAG%
                docker run -d -p 3000:80  --name frontend %FRONTEND_IMAGE%:%TAG%
                '''
            }
        }
    }

    post {
        always {
            echo Cleaning node_modules safely
            bat 'rmdir /s /q frontend\\node_modules 2>nul || exit 0'
        }
        success {
            echo '✅ Jenkins Full Stack Pipeline Success'
        }
        failure {
            echo '❌ Jenkins Pipeline Failed'
        }
    }
}
