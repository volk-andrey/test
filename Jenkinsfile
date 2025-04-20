pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                script {
                    withYaml('config.yaml') {
                        echo "Target: ${target.name}"
                        echo "Source: ${source.name}"
                    }
                }
            }
        }
    }
}