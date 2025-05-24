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
                    docker.build("${IMAGE_NAME}:${imageTag}")
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {
                script {
                    def imageTag = "${env.BUILD_NUMBER}"
                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKERHUB_CREDENTIALS') {
                        docker.image("${IMAGE_NAME}:${imageTag}").push()
                        docker.image("${IMAGE_NAME}:${imageTag}").push('latest')
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    // הורדת docker-compose.yaml עדכני
                    sh 'curl -O https://raw.githubusercontent.com/oren1984/flask-jenkins-pipeline/main/docker-compose.yaml'
                    // הרצת השירות עם docker-compose
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
