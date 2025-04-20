pipeline {
    agent any
    parameters {
        booleanParam(
            name: 'UPDATE_PARAMS',
            defaultValue: false,
            description: 'Обновить параметры из YAML?'
        )
        choice(
            name: 'CLUSTER_NAME',
            choices: ['cluster1', 'cluster2'], // Автоматически можно заполнить из YAML
            description: 'Выберите кластер из config.yaml'
        )
        // Эти параметры заполняются автоматически и не редактируются вручную
        string(name: 'TARGET_NAME', defaultValue: '', description: 'Название целевого кластера')
        string(name: 'TARGET_BOOTSTRAP', defaultValue: '', description: 'Bootstrap целевого кластера')
        string(name: 'SOURCE_NAME', defaultValue: '', description: 'Название исходного кластера')
        string(name: 'SOURCE_BOOTSTRAP', defaultValue: '', description: 'Bootstrap исходного кластера')
    }

    stages {
        stage('Update Parameters') {
            when {
                expression { params.UPDATE_PARAMS.toBoolean() }
            }
            steps {
                script {
                    // Чтение конфига
                    def config = readYaml file: 'config.yaml'
                    def cluster = config.clusters[params.CLUSTER_NAME]

                    // Обновление параметров
                    properties([
                        parameters([
                            booleanParam(name: 'UPDATE_PARAMS', defaultValue: false),
                            choice(name: 'CLUSTER_NAME', choices: ['cluster1', 'cluster2']),
                            string(name: 'TARGET_NAME', defaultValue: cluster.target.name),
                            string(name: 'TARGET_BOOTSTRAP', defaultValue: cluster.target.bootstrap),
                            string(name: 'SOURCE_NAME', defaultValue: cluster.source.name),
                            string(name: 'SOURCE_BOOTSTRAP', defaultValue: cluster.source.bootstrap)
                        ])
                    ])

                    echo "Параметры кластера ${params.CLUSTER_NAME} обновлены!"
                }
            }
        }

        stage('Main Pipeline') {
            when {
                expression { !params.UPDATE_PARAMS.toBoolean() }
            }
            steps {
                script {
                    echo """
                    Конфигурация:
                    - Кластер: ${params.CLUSTER_NAME}
                    - Target: ${params.TARGET_NAME} (${params.TARGET_BOOTSTRAP})
                    - Source: ${params.SOURCE_NAME} (${params.SOURCE_BOOTSTRAP})
                    """
                    // Ваша основная логика здесь...
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