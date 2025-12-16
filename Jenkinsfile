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
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t ${BACKEND_IMAGE}:${TAG} backend
                docker build -t ${FRONTEND_IMAGE}:${TAG} frontend
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-pass', variable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u vedasamhitha17 --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                docker push ${BACKEND_IMAGE}:${TAG}
                docker push ${FRONTEND_IMAGE}:${TAG}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f backend frontend || true
                docker run -d -p 8080:8080 --name backend ${BACKEND_IMAGE}:${TAG}
                docker run -d -p 3000:80  --name frontend ${FRONTEND_IMAGE}:${TAG}
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Jenkins Full Stack Pipeline Success'
        }
        failure {
            echo '❌ Jenkins Pipeline Failed'
        }
    }
}
