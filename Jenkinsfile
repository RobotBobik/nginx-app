pipeline {
    agent any
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
        stage('Stop and Remove Old Container') {
            steps {
                // Зупиняємо та видаляємо старий контейнер, якщо він існує
                sh '''
                docker ps -q --filter "name=nginx-app-container" | xargs -r docker stop
                docker ps -aq --filter "name=nginx-app-container" | xargs -r docker rm
                '''
            }
        }
        stage('Wait for Container Shutdown') {
            steps {
                // Чекаємо декілька секунд, щоб переконатися, що старий контейнер зупинений
                sleep time: 10, unit: 'SECONDS'
            }
        }
        stage('Run New Container') {
            steps {
                // Запускаємо новий контейнер
                sh 'docker run -d --name nginx-app-container -p 8080:80 nginx-app:latest'
            }
        }
    }
}
