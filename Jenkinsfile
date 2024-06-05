pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = '5db28b23-a2e3-4f94-8521-3222d78dafd6' // Replace with your actual credentials ID
        DOCKER_CREDENTIALS_ID = '1541159d-cb4b-46b9-97c1-4e1ff271a42b' // Replace with your actual Docker Hub credentials ID
        DOCKER_IMAGE = 'discoverdevops/my-python-flask-app' // Replace with your Docker Hub username and image name
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/chrahul/my-python-flask-app.git',
                        credentialsId: env.GIT_CREDENTIALS_ID
                    ]]
                ])
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Code Linting') {
            steps {
                sh 'flake8 .'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', env.DOCKER_CREDENTIALS_ID) {
                        dockerImage.push("${env.BUILD_ID}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        always {
            junit 'reports/*.xml'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}