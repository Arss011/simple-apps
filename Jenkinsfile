pipeline {
    agent { label 'docker-dicky' }
    
    tools {nodejs "Nodejs-18.16.0"}

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/Arss011/simple-apps.git'
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
                sh '''cd apps
                sonar-scanner \
                -Dsonar.projectKey=simple-apps \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://172.23.15.67:9000 \
                -Dsonar.login=squ_d81d1efd61743b14d99266316f5cf832c52b1d58'''
            }
        }
        stage('Deploy compose Container') {
            steps {
                sh '''
                docker compose down\
                docker image prune
                docker compose build
                docker compose up -d
                '''
            }
        }
    }
}
