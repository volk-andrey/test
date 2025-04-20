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
            choices: ['NOT_LOADED'], // Временное значение
            description: 'Выберите кластер'
        )
        string(
            name: 'TARGET_NAME',
            defaultValue: '',
            description: 'Название целевого кластера'
        )
        string(
            name: 'TARGET_BOOTSTRAP',
            defaultValue: '',
            description: 'Bootstrap серверы'
        )
    }
    
    stages {
        stage('Load Configuration') {
            steps {
                script {
                    // Проверяем наличие файла
                    if (!fileExists('config.yaml')) {
                        error "Файл config.yaml не найден!"
                    }
                    
                    // Читаем конфиг
                    def config = readYaml file: 'config.yaml'
                    def clusters = config.clusters.keySet() as List
                    
                    // Если нужно обновить параметры или они не загружены
                    if (params.UPDATE_PARAMS || params.CLUSTER_NAME == 'NOT_LOADED') {
                        // Выбираем первый кластер по умолчанию
                        def selectedCluster = clusters[0] 
                        def clusterConfig = config.clusters[selectedCluster]
                        
                        // Обновляем параметры через properties
                        properties([
                            parameters([
                                booleanParam(name: 'UPDATE_PARAMS', defaultValue: false),
                                choice(name: 'CLUSTER_NAME', choices: clusters),
                                string(name: 'TARGET_NAME', defaultValue: clusterConfig.target.name),
                                string(name: 'TARGET_BOOTSTRAP', defaultValue: clusterConfig.target.bootstrap)
                            ])
                        ])
                        
                        echo "Параметры обновлены для кластера: ${selectedCluster}"
                    }
                }
            }
        }
        
        stage('Main Pipeline') {
            when {
                expression { !params.UPDATE_PARAMS && params.CLUSTER_NAME != 'NOT_LOADED' }
            }
            steps {
                script {
                    echo """
                    ===== Конфигурация =====
                    Кластер: ${params.CLUSTER_NAME}
                    Target: ${params.TARGET_NAME}
                    Bootstrap: ${params.TARGET_BOOTSTRAP}
                    """
                    
                    // Ваша основная логика здесь
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