pipeline {
    agent any
    parameters {
        booleanParam(name: 'UPDATE_PARAMS', defaultValue: false, description: 'Обновить параметры из YAML?')
        string(name: 'TARGET_NAME', defaultValue: '', description: 'Название целевого кластера Kafka')
        string(name: 'TARGET_BOOTSTRAP', defaultValue: '', description: 'Bootstrap-серверы целевого кластера')
        string(name: 'SOURCE_NAME', defaultValue: '', description: 'Название исходного кластера Kafka')
        string(name: 'SOURCE_BOOTSTRAP', defaultValue: '', description: 'Bootstrap-серверы исходного кластера')
    }

    stages {
        stage('Check Params Update') {
            when { 
                beforeAgent true
                expression { 
                    return params.UPDATE_PARAMS.toBoolean() 
                } 
            }
            steps {
                script {
                    try {
                        // 1. Чтение YAML (безопасный метод)
                        def kafkaConfig = readYaml file: 'config.yaml'
                        assert kafkaConfig != null : "YAML config is empty"
                        
                        // 2. Обновление параметров (безопасный метод)
                        def job = currentBuild.rawBuild.parent
                        def newParams = [
                            string(name: 'TARGET_NAME', value: kafkaConfig.target?.name ?: ''),
                            string(name: 'TARGET_BOOTSTRAP', value: kafkaConfig.target?.bootstrap ?: ''),
                            string(name: 'SOURCE_NAME', value: kafkaConfig.source?.name ?: ''),
                            string(name: 'SOURCE_BOOTSTRAP', value: kafkaConfig.source?.bootstrap ?: ''),
                            booleanParam(name: 'UPDATE_PARAMS', value: false)
                        ]
                        properties([parameters(newParams)])
                        
                        echo "Параметры успешно обновлены"
                    } catch (Exception e) {
                        error "Ошибка обновления параметров: ${e.getMessage()}"
                    }
                }
            }
        }

        stage('Main Pipeline') {
            when { expression { !params.UPDATE_PARAMS.toBoolean() } }
            steps {
                script {
                    echo """
                    Работа с параметрами:
                    - TARGET_NAME: ${params.TARGET_NAME}
                    - TARGET_BOOTSTRAP: ${params.TARGET_BOOTSTRAP}
                    """
                    // Основная логика пайплайна
                }
            }
        }
    }
}
// pipeline {
//     agent {
//         kubernetes {
//             yaml '''
//                 apiVersion: v1
//                 kind: Pod
//                 spec:
//                     containers:
//                     - name: python
//                       image: python:3.9-slim
//                       command: ["sleep"]
//                       args: ["infinity"]
//             '''
//         }
//     }
//     stages {
//         stage('Test') {
//             steps {
//                 container('python') {
//                     sh 'pip install pyyaml'
//                     sh 'python test.py'
//                 }
//             }
//         }
//     }
// }