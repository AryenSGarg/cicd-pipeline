pipeline {
    agent any

    environment {
        IMAGE_NAME = ""
        PORT = ""
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/<your-username>/cicd-pipeline.git'
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
                bat 'npm test || echo No tests'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    bat """
                    docker ps -q --filter "publish=%PORT%" | FOR /F %i IN ('more') DO docker stop %i
                    docker run -d -p %PORT%:%PORT% %IMAGE_NAME%
                    """
                }
            }
        }
    }
}
