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
            when { expression { params.UPDATE_PARAMS == true } }
            steps {
                script {
                    // Читаем YAML-конфиг (Python или readYaml)
                    def kafkaConfig = readYaml file: 'config.yaml'

                    // Обновляем параметры джобы
                    def job = Jenkins.instance.getItemByFullName(env.JOB_NAME)
                    def newParams = [
                        new StringParameterValue('TARGET_NAME', kafkaConfig.target.name),
                        new StringParameterValue('TARGET_BOOTSTRAP', kafkaConfig.target.bootstrap),
                        new StringParameterValue('SOURCE_NAME', kafkaConfig.source.name),
                        new StringParameterValue('SOURCE_BOOTSTRAP', kafkaConfig.source.bootstrap),
                        new BooleanParameterValue('UPDATE_PARAMS', false)  // Сбрасываем флаг
                    ]
                    job.addOrReplaceAction(new ParametersAction(newParams))
                    job.save()

                    echo "Параметры обновлены из config.yaml"
                    currentBuild.result = 'SUCCESS'  // Завершаем сборку без ошибок
                }
            }
        }

        stage('Main Pipeline') {
            when { expression { params.UPDATE_PARAMS == false } }
            steps {
                echo """
                    Работа с параметрами:
                    - TARGET_NAME: ${params.TARGET_NAME}
                    - TARGET_BOOTSTRAP: ${params.TARGET_BOOTSTRAP}
                """
                // Ваши основные шаги здесь...
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