pipeline {
    agent any
    environment {
        IMAGE_NAME = "sakshi2105/python-app"  // DockerHub username sakshi2105
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Sakshideshmukh21/python_app.git', 
                    branch: 'master', 
                    credentialsId: 'github-pat'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${env.BUILD_ID}")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image("${IMAGE_NAME}:${env.BUILD_ID}").inside {
                        sh 'python3 --version'
                    }
                }
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image("${IMAGE_NAME}:${env.BUILD_ID}").push()
                        docker.image("${IMAGE_NAME}:${env.BUILD_ID}").push('latest')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'docker stop python-app || true && docker rm python-app || true'
                    sh "docker run -d --name python-app -p 5000:5000 ${IMAGE_NAME}:latest"
                }
            }
        }
    }
    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
