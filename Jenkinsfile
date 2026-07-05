pipeline {
    agent any

    environment {
        IMAGE_NAME = "bhushan432/news"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Bhushanrajput/news.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
                docker stop news-container
                exit /b 0
                '''
            }
        }

        stage('Remove Old Container') {
            steps {
                bat '''
                docker rm news-container
                exit /b 0
                '''
            }
        }

        stage('Pull Latest Image') {
            steps {
                bat 'docker pull %IMAGE_NAME%'
            }
        }

        stage('Run New Container') {
            steps {
                bat 'docker run -d -p 8082:80 --name news-container %IMAGE_NAME%'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }

        failure {
            echo 'Pipeline execution failed!'
        }
    }
}