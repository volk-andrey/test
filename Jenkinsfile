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
                container('python') {
                    sh 'pip install pyyaml'
                    sh 'python test.py'
                }
            }
        }
    }
}