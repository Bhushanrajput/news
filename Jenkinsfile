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

                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }

        stage('Deploy Kubernetes') {
            steps {

                bat 'kubectl apply -f deployment.yaml'

                bat 'kubectl apply -f service.yaml'

                bat 'kubectl rollout restart deployment/news-deployment'
            }
        }

        stage('Verify Deployment') {
            steps {

                bat 'kubectl get deployments'

                bat 'kubectl get pods'

                bat 'kubectl get svc'
            }
        }
    }
}