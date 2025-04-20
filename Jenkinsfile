pipeline {
    agent any
    parameters {
        booleanParam(name: 'UPDATE_PARAMS', defaultValue: false, description: 'Обновить параметры из YAML?')
        string(name: 'TARGET_NAME', defaultValue: '', description: 'Название целевого кластера Kafka')
        string(name: 'TARGET_BOOTSTRAP', defaultValue: '', description: 'Bootstrap-серверы целевого кластера')
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
                        // Проверяем наличие файла
                        if (!fileExists('config.yaml')) {
                            error "config.yaml not found"
                        }
                        
                        // Читаем YAML
                        def kafkaConfig = readYaml file: 'config.yaml'
                        
                        // Безопасное обновление параметров через properties
                        properties([
                            parameters([
                                string(name: 'TARGET_NAME', value: kafkaConfig.target?.name ?: ''),
                                string(name: 'TARGET_BOOTSTRAP', value: kafkaConfig.target?.bootstrap ?: ''),
                                booleanParam(name: 'UPDATE_PARAMS', value: false)
                            ])
                        ])
                        
                        echo "Параметры успешно обновлены"
                    } catch (Exception e) {
                        error "Ошибка обновления параметров: ${e.getMessage()}"
                    }
                }
            }
        }

        stage('Main Pipeline') {
            when { 
                expression { 
                    return !params.UPDATE_PARAMS.toBoolean() 
                } 
            }
            steps {
                echo "Основной пайплайн"
                echo "TARGET_NAME: ${params.TARGET_NAME}"
                echo "TARGET_BOOTSTRAP: ${params.TARGET_BOOTSTRAP}"
                // Ваша основная логика здесь
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