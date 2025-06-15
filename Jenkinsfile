pipeline {
    agent { label 'docker-dicky' }
    
    tools { nodejs "Nodejs-18.16.0" }

    environment {
        // --- Konfigurasi Repositori ---
        GIT_REPO_URL          = 'https://github.com/Arss011/simple-apps.git'
        
        // --- Konfigurasi Docker ---
        DOCKER_CREDENTIALS_ID = 'dckr_pat_R7kkH0WUbbASQDZ9Xi3ddqrFrSA'
        DOCKER_HUB_USER   = 'arss011'
        DOCKER_IMAGE_NAME     = 'arss011/simple-apps-apps'
        IMAGE_TAG             = 'v1.0'
        COMPOSE_SERVICE_NAME  = 'apps' 
        
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
        
        stage('Code Review With Sonarqube') {
            steps {
                sh '''sonar-scanner \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.sources=. \
                -Dsonar.host.url=${SONAR_HOST_URL}\
                -Dsonar.login=${SONAR_TOKEN_ID}'''
            }
        }
        
        stage('Deploy compose Container') {
            steps {
                sh '''
                docker compose down
                docker image prune
                docker compose build
                docker compose up -d
                '''
            }
        }
        stage('Upload to Regestry Image') {
            steps {
                sh '''
                docker tag ${DOCKER_IMAGE_NAME} ${DOCKER_HUB_USER}/${DOCKER_IMAGE_NAME}:v1.0
                docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE_NAME}:${VERSION}
                '''
            }
        }
    }
}