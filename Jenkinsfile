pipeline {
    agent { label 'docker-esa' }
    
    tools {nodejs "NodeJs-18.16"}
    
    environment {
        GIT_BRANCH      = 'main'
        GIT_REPO        = 'https://github.com/esaanugraha/simple-apps.git'
        SONAR_HOST      = 'http://172.23.15.73:9000'
        SONAR_PROJECT   = 'simple-apps'
        SONAR_TOKEN     = 'squ_640fa85ea57ef2618a3da5840dbce7400a6ff020'
        IMAGE_NAME      = 'simple-apps-apps'
        DOCKER_HUB_USER = 'esanugraha'
        VERSION         = 'v1.0'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"
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
        stage('Code Review With Sonarqube') {
            steps {
                sh '''sonar-scanner \
                -Dsonar.projectKey=${SONAR_PROJECT} \
                -Dsonar.sources=. \
                -Dsonar.host.url= ${SONAR_HOST}\
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
        stage('Upload to Regestry Image') {
            steps {
                sh '''
                docker tag ${IMAGE_NAME} ${DOCKER_HUB_USER}/${IMAGE_NAME}:v1.0
                docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${VERSION}
                '''
            }
        }
    }
}
