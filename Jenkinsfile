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
            choices: ['NOT_LOADED'], // Временное значение, будет перезаписано
            description: 'Выберите кластер из config.yaml'
        )
        string(
            name: 'TARGET_NAME',
            defaultValue: '',
            description: 'Название целевого кластера (автозаполнение)',
            trim: true
        )
        string(
            name: 'TARGET_BOOTSTRAP',
            defaultValue: '',
            description: 'Bootstrap целевого кластера (автозаполнение)',
            trim: true
        )
    }
    
    stages {
        stage('Load Configuration') {
            steps {
                script {
                    // Проверяем наличие файла конфигурации
                    if (!fileExists('config.yaml')) {
                        error "Файл config.yaml не найден в workspace!"
                    }
                    
                    // Читаем конфигурацию с обработкой ошибок
                    try {
                        def config = readYaml file: 'config.yaml'
                        env.CLUSTER_NAMES = config.clusters.keySet().join(',')
                        
                        // Если это первый запуск или обновление параметров
                        if (params.UPDATE_PARAMS || params.CLUSTER_NAME == 'NOT_LOADED') {
                            def clusterNames = config.clusters.keySet() as List
                            def firstCluster = clusterNames[0]
                            def clusterConfig = config.clusters[firstCluster]
                            
                            // Обновляем параметры
                            currentBuild.rawBuild.getAction(ParametersAction.class)?.replace(
                                new StringParameterValue('CLUSTER_NAME', firstCluster),
                                new StringParameterValue('TARGET_NAME', clusterConfig.target.name),
                                new StringParameterValue('TARGET_BOOTSTRAP', clusterConfig.target.bootstrap),
                                new BooleanParameterValue('UPDATE_PARAMS', false)
                            )
                        }
                    } catch (Exception e) {
                        error "Ошибка чтения config.yaml: ${e.getMessage()}"
                    }
                }
            }
        }
        
        stage('Validate Cluster') {
            when {
                expression { !params.UPDATE_PARAMS }
            }
            steps {
                script {
                    // Проверяем, что параметры кластера валидны
                    if (params.CLUSTER_NAME == 'NOT_LOADED' || !params.TARGET_NAME || !params.TARGET_BOOTSTRAP) {
                        error """
                        Параметры кластера не загружены!
                        Запустите сборку с параметром UPDATE_PARAMS=true
                        """
                    }
                    
                    echo "Выбран кластер: ${params.CLUSTER_NAME}"
                }
            }
        }
        
        stage('Main Pipeline') {
            when {
                expression { !params.UPDATE_PARAMS }
            }
            steps {
                script {
                    echo """
                    ===== Конфигурация кластера =====
                    Кластер: ${params.CLUSTER_NAME}
                    Target Name: ${params.TARGET_NAME}
                    Target Bootstrap: ${params.TARGET_BOOTSTRAP}
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