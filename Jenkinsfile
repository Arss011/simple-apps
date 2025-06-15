pipeline {
    agent { label 'docker-dicky' }
    
    tools { nodejs "Nodejs-18.16.0" }

    environment {
        GIT_REPO_URL          = 'https://github.com/Arss011/simple-apps.git'
        
        DOCKER_CREDENTIALS_ID = 'arss011'
        DOCKER_IMAGE_NAME     = 'simple-apps-apps'
        IMAGE_TAG             = 'v1.0'
        
        SONAR_HOST_URL        = 'http://172.23.15.67:9000'
        SONAR_PROJECT_KEY     = 'simple-apps'
        SONAR_TOKEN_ID        = 'squ_d81d1efd61743b14d99266316f5cf832c52b1d58'
        
        DOCKER_HUB_USER       = 'arss011'
        VERSION               = 'v1.0'
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
                sh '''sonar-scanner \
                -Dsonar.projectKey=simple-apps \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://172.23.15.67:9000 \
                -Dsonar.login=${SONAR_TOKEN_ID}'''
            }
        }
        
        stage('Upload to Regestry Image') {
            steps {
                sh '''
                docker tag ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_CREDENTIALS_ID}/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
                docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
}