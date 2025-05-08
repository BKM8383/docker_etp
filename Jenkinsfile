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
                withDockerRegistry([ credentialsId: "${DOCKER_CREDENTIALS_ID}", url: '' ]) {
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
                    sh '''
                    docker stop nodejs-container || true
                    docker rm nodejs-container || true
                    docker run -d --name nodejs-container -p 3000:3000 ${DOCKER_IMAGE}:${BUILD_NUMBER}
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
