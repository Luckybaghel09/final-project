pipeline {
    agent any

    environment {
        IMAGE_NAME = "lokesh0902/final-project"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        WEB1 = "13.232.93.59"
        WEB2 = "43.205.212.24"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Luckybaghel09/final-project.git'
            }
        }

        stage('Build Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:latest ."
            }
        }

        stage('Docker Login') {
            steps {
                sh '''
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push $IMAGE_NAME:latest"
            }
        }

        stage('Deploy Web1') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@$WEB1 '
                docker pull $IMAGE_NAME:latest &&
                docker stop website || true &&
                docker rm website || true &&
                docker run -d -p 80:80 --name website $IMAGE_NAME:latest
                '
                """
            }
        }

        stage('Deploy Web2') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@$WEB2 '
                docker pull $IMAGE_NAME:latest &&
                docker stop website || true &&
                docker rm website || true &&
                docker run -d -p 80:80 --name website $IMAGE_NAME:latest
                '
                """
            }
        }
    }
}
