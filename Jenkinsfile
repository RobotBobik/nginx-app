pipeline {
    agent { label 'master' }
    stages {
        stage('Checkout') {
            steps {
                // Клонуємо репозиторій та оновлюємо останні зміни з main
                git branch: 'main', url: 'https://github.com/RobotBobik/nginx-app.git'
                sh 'git pull origin main'  // Переконатися, що ми отримали останні зміни
            }
        }
        stage('Build Docker Image') {
            steps {
                // Білдимо образ з тегом "nginx-app:latest"
                sh 'docker build -t nginx-app:latest .'
            }
        }
        stage('Stop and Remove All Containers') {
            steps {
                // Зупиняємо всі контейнери та
                sh '''
                docker stop $(docker ps -a -q) || true
                docker rm $(docker ps -aq) || true
                '''
            }
        }
        stage('Wait for Containers Shutdown') {
            steps {
                // Чекаємо декілька секунд, щоб переконатися, що всі контейнери зупинені
                sleep time: 10, unit: 'SECONDS'
            }
        }
        stage('Run New Container') {
            steps {
                // Запускаємо новий контейнер, але на іншому порту, щоб уникнути конфлікту з ngrok
                sh 'docker run -d --name nginx-app-container -p 8081:80 nginx-app:latest'
            }
        }
    }
}
