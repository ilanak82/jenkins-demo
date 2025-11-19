pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "my-web-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                }
            }
        }
        
        stage('Test Image') {
            steps {
                echo 'Testing Docker image...'
                script {
                    sh "docker images | grep ${DOCKER_IMAGE}"
                }
            }
        }
        
        stage('Run Container') {
            steps {
                echo 'Running container for testing...'
                script {
                    sh 'docker rm -f test-container 2>/dev/null || true'
                    
                    sh "docker run -d --name test-container -p 8081:80 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    
                    sh '''
                        echo "Waiting for container to be ready..."
                        sleep 3
                        
                        for i in 1 2 3 4 5 6 7 8 9 10; do
                            if docker exec test-container wget -q -O- http://localhost 2>/dev/null; then
                                echo "✓ Container is ready and responding!"
                                exit 0
                            fi
                            echo "Attempt $i: Container not ready yet, waiting..."
                            sleep 2
                        done
                        echo "✗ Container failed to become ready"
                        exit 1
                    '''
                    
                    echo "Container is running successfully!"
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                echo 'Cleaning up test container...'
                script {
                    sh 'docker stop test-container || true'
                    sh 'docker rm test-container || true'
                }
            }
        }
    }
    
    post {
        success {
            echo "✅ Pipeline completed! Docker image ${DOCKER_IMAGE}:${DOCKER_TAG} is ready!"
        }
        failure {
            echo '❌ Pipeline failed!'
            script {
                sh 'docker stop test-container 2>/dev/null || true'
                sh 'docker rm test-container 2>/dev/null || true'
            }
        }
    }
}