pipeline {
    agent { label 'docker-dicky' }
    
    tools { nodejs "Nodejs-18.16.0" }

    environment {
        // --- Konfigurasi Repositori ---
        GIT_REPO_URL          = 'https://github.com/Arss011/simple-apps.git'
        
        // --- Konfigurasi Docker ---
        // ID kredensial Docker Hub yang ada di Jenkins. WAJIB BENAR!
        DOCKER_CREDENTIALS_ID = 'dckr_pat_R7kkH0WUbbASQDZ9Xi3ddqrFrSA'
        // Nama lengkap image di Docker Hub (username/repo)
        DOCKER_IMAGE_NAME     = 'arss011/simple-apps-apps'
        IMAGE_TAG             = 'v1.0'
        // Nama service yang ada di file docker-compose.yml
        COMPOSE_SERVICE_NAME  = 'apps' 
        
        // --- Konfigurasi SonarQube ---
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
        
        // Menggabungkan Build, Deploy, dan Push dalam satu stage yang aman
        stage('Build and Push to Docker Registry') {
            steps {
                // MEMBUNGKUS SEMUA PERINTAH DOCKER DENGAN BLOK INI
                withDockerRegistry(credentialsId: env.DOCKER_CREDENTIALS_ID) {
                    // MENGGUNAKAN KUTIP GANDA """...""" AGAR VARIABEL TERBACA
                    sh """
                    docker compose down
                    docker image prune -f
                    docker compose build
                    
                    # Memberi tag pada image yang di-build oleh compose.
                    # Asumsinya nama service di docker-compose adalah 'apps'.
                    # Ini akan membuat tag seperti 'arss011/simple-apps-apps:v1.0'.
                    docker tag ${env.COMPOSE_SERVICE_NAME}:latest ${env.DOCKER_IMAGE_NAME}:${env.IMAGE_TAG}
                    
                    # Mendorong image yang sudah diberi tag ke Docker Hub.
                    docker push ${env.DOCKER_IMAGE_NAME}:${env.IMAGE_TAG}
                    """
                }
            }
        }
    }
}