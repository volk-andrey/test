pipeline {
    agent {
        docker { image 'python:3.9-slim' }  // Официальный образ Python
    }
    stages {
        stage('Test') {
            steps {
                script {
                    sh 'python test.py'
                }
            }
        }
    }
}