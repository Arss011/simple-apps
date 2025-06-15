pipeline {
    agent { label 'docker-dicky' }
    
    tools { nodejs "Nodejs-18.16.0" }

    environment {
        // --- Konfigurasi Repositori ---
        GIT_REPO_URL          = 'https://github.com/Arss011/simple-apps.git'
        
        // --- Konfigurasi Docker ---
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE_NAME     = 'arss011/simple-apps-apps'
        IMAGE_TAG             = 'v1.0'
        
        SONAR_HOST_URL        = 'http://172.23.15.67:9000'
        SONAR_PROJECT_KEY     = 'simple-apps'
        SONAR_TOKEN_ID        = 'squ_d81d1efd61743b14d99266316f5cf832c52b1d58'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: env.GIT_REPO_URL
            }
        }
        
        stage('Build Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Testing') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Code Review with Sonarqube') {
            steps {
                withCredentials([string(credentialsId: env.SONAR_TOKEN_ID, variable: 'SONAR_SECRET_TOKEN')]) {
                    sh """sonar-scanner \
                       -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
                       -Dsonar.sources=. \
                       -Dsonar.host.url=${env.SONAR_HOST_URL} \
                       -Dsonar.login=${SONAR_SECRET_TOKEN}"""
                }
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: env.DOCKER_CREDENTIALS_ID) {
                    sh """
                    docker compose down
                    docker image prune -f
                    
                    docker compose build
                    
                    docker compose up -d
                    
                    docker tag simple-apps-apps:latest ${env.DOCKER_IMAGE_NAME}:${env.IMAGE_TAG}
                    
                    docker push ${env.DOCKER_IMAGE_NAME}:${env.IMAGE_TAG}
                    """
                }
            }
        }
    }
}