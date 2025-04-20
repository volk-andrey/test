pipeline {
    agent {
        kubernetes {
            yaml '''
                apiVersion: v1
                kind: Pod
                spec:
                    containers:
                    - name: python
                      image: python:3.9-slim
                      command: ["sleep"]
                      args: ["infinity"]
            '''
        }
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