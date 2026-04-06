pipeline {
    agent any

    environment {
        IMAGE_NAME = ""
        PORT = ""
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/AryenSGarg/cicd-pipeline.git'
            }
        }

        stage('Set Environment') {
            steps {
                script {
                    if (env.BRANCH_NAME == "main") {
                        env.PORT = "3000"
                        env.IMAGE_NAME = "nodemain:v1.0"
                    } else {
                        env.PORT = "3001"
                        env.IMAGE_NAME = "nodedev:v1.0"
                    }
                    echo "Deploying ${env.IMAGE_NAME} on port ${env.PORT}"
                }
            }
        }

        stage('Build') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test || echo No tests found'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${env.IMAGE_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Stop old container with same image name if exists
                    bat """
                    for /f "tokens=*" %%i in ('docker ps -q --filter "ancestor=${env.IMAGE_NAME}"') do docker stop %%i
                    docker run -d -p ${env.PORT}:${env.PORT} --name ${env.IMAGE_NAME} ${env.IMAGE_NAME}
                    """
                }
            }
        }
    }
}
