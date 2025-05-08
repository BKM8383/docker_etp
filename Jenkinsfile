pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'bkm83/nodejs-app'
        DOCKER_CREDENTIALS_ID = '295747ed-813f-4ee5-90cb-1701f932606e' // Stored in Jenkins credentials
        TEST_SERVER = 'user@test-server-ip'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/yourusername/your-nodejs-repo.git'
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

        stage('Deploy') {
            steps {
                script {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${TEST_SERVER} << EOF
                    docker pull ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    docker stop nodejs-container || true
                    docker rm nodejs-container || true
                    docker run -d --name nodejs-container -p 3000:3000 ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    EOF
                    """
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
