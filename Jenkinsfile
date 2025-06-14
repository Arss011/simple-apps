pipeline {
    agent { label 'docker-esa' }
    
    tools {nodejs "NodeJs-18.16"}

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/esaanugraha/simple-apps.git'
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
                -Dsonar.projectKey=simple-apps \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://172.23.15.73:9000 \
                -Dsonar.login=squ_640fa85ea57ef2618a3da5840dbce7400a6ff020'''
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
                docker tag simple-apps-apps esanugraha/simple-apps-apps:v1.0
                docker push esanugraha/simple-apps-apps:v1.0
                '''
            }
        }
    }
}
