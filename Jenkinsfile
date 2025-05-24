pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = 'oren1984/my-flask-api'
    }

    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: 'https://github.com/oren1984/flask-jenkins-pipeline.git'
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    def imageTag = "${env.BUILD_NUMBER}"
                    appImage = docker.build("${IMAGE_NAME}:${imageTag}")
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        appImage.push()
                        appImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'curl -O https://raw.githubusercontent.com/oren1984/flask-jenkins-pipeline/main/docker-compose.yaml'
                    sh 'docker-compose down || true'
                    sh 'docker-compose up -d'
                }
            }
        }

        stage('Validation') {
            steps {
                script {
                    sh 'curl -f http://localhost:5000'
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
        }
        failure {
            echo "Build failed!"
        }
    }
}
