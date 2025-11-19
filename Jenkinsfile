pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from Git...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'echo "Build timestamp: $(date)" > build-info.txt'
                sh 'ls -la'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh '''
                    if [ -f index.html ]; then
                        echo "✓ index.html exists"
                    else
                        echo "✗ index.html missing"
                        exit 1
                    fi
                '''
            }
        }
        
        stage('Report') {
            steps {
                echo 'Generating report...'
                sh 'cat build-info.txt'
                sh 'echo "All tests passed!"'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully! ✅'
        }
        failure {
            echo 'Pipeline failed! ❌'
        }
    }
}