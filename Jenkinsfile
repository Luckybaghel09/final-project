pipeline {
    agent any
 
    environment {
        DOCKER_IMAGE = "yourdockerhubusername/final-project"
        IMAGE_TAG    = "${env.BUILD_NUMBER}"
        DEPLOY_HOST  = "your.server.ip.or.hostname"
        DEPLOY_USER  = "ubuntu"
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Luckybaghel09/final-project.git',
                    credentialsId: 'lokesh0902'
            }
        }
 
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} -t ${DOCKER_IMAGE}:latest ."
            }
        }
 
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo "\$DOCKER_PASS" | docker login -u "\$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                        docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
 
        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['jenkins']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} '
                            docker pull ${DOCKER_IMAGE}:latest &&
                            docker stop final-project || true &&
                            docker rm final-project || true &&
                            docker run -d --name final-project -p 80:80 ${DOCKER_IMAGE}:latest
                        '
                    """
                }
            }
        }
    }
 
    post {
        always {
            sh 'docker logout || true'
        }
        success {
            echo "✅ Build & Deploy successful: ${DOCKER_IMAGE}:${IMAGE_TAG}"
        }
        failure {
            echo "❌ Build failed. Check console output above."
        }
    }
}
 
