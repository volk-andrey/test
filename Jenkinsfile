pipeline {
    agent {
        label 'linux && docker' // запускать только на агентах с этими метками
    }
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