pipeline {
    agent { label 'docker-dicky' }
    
    tools {nodejs "Nodejs-18.16.0"}

    environtment {
        GIT_BRANCH = 'main'
        GIT_REPO = 'https://github.com/Arss011/simple-apps.git'
        GIT_CREDENTIAL = 'github'

        DOCKERHUB_CREDENTIAL = 'arss011'
        DOCKERHUB_REPO = 'Arss011/simple-apps-apps'
        DOCKERHUB_TAG = 'v1.0'

        SONAR_HOST = 'http://172.23.15.67:9000'
        SONAR_PROJECT_KEY = 'simple-apps'
        SONAR_TOKEN = 'squ_d81d1efd61743b14d99266316f5cf832c52b1d58'

        IMAGE_NAME = 'simple-apps-apps'
        IMAGE_TAG = 'v1.0'

        VERSION: '1.0'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: '${GIT_BRANCH}', url: '${GIT_REPO}'
            }
        }
        stage('Build') {
            steps {
                sh '''npm install'''
            }
        }
        stage('Testing') {
            steps {
                sh '''npm test'''
            }
        }
        stage('Code Review with Sonarqube') {
            steps {
                sh '''sonar-scanner \
                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                -Dsonar.sources=. \
                -Dsonar.host.url=${SONAR_HOST} \
                -Dsonar.login=${SONAR_TOKEN}'''
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
        stage('Upload to Registry Image') {
            steps {
                sh '''
                docker tag ${IMAGE_NAME} ${DOCKERHUB_CREDENTIAL}/${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${DOCKERHUB_CREDENTIAL}/${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
            
        }
    }
}
