pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'bkm83/your-nodejs-app'
        DOCKER_CREDENTIALS_ID = 'dockerhub-creds'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/bkm8383/docker_etp.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push') {
            steps {
                withDockerRegistry([credentialsId: "${DOCKER_CREDENTIALS_ID}", url: '']) {
                    script {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                script {
                    bat '''
                    echo "Stopping and removing old container if exists..."
                    docker stop nodejs-container || exit 0
                    docker rm nodejs-container || exit 0

                    echo "Running new container..."
                    docker run -d --name nodejs-container -p 3000:3000 bkm83/your-nodejs-app:%BUILD_NUMBER%

                    echo "Currently running containers:"
                    docker ps
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
