// Сначала определяем общие методы для работы с конфигом
def loadClusterNames() {
    try {
        def config = readYaml file: 'config.yaml'
        return config.clusters.keySet() as List
    } catch (Exception e) {
        echo "Ошибка чтения config.yaml: ${e.getMessage()}"
        return ["ERROR: Не могу загрузить кластеры"]
    }
}

def getClusterConfig(clusterName) {
    try {
        def config = readYaml file: 'config.yaml'
        return config.clusters[clusterName]
    } catch (Exception e) {
        echo "Ошибка чтения конфига кластера: ${e.getMessage()}"
        return null
    }
}

properties([
    parameters([
        booleanParam(
            name: 'UPDATE_PARAMS',
            defaultValue: false,
            description: 'Обновить параметры из YAML?'
        ),
        choice(
            name: 'CLUSTER_NAME',
            choices: loadClusterNames(),
            description: 'Выберите кластер из config.yaml'
        ),
        string(
            name: 'TARGET_NAME',
            defaultValue: '',
            description: 'Название целевого кластера (автозаполнение)'
        ),
        string(
            name: 'TARGET_BOOTSTRAP',
            defaultValue: '',
            description: 'Bootstrap целевого кластера (автозаполнение)'
        )
    ])
])

pipeline {
    agent any
    
    stages {
        stage('Initialize') {
            steps {
                script {
                    // Проверяем наличие файла конфига
                    if (!fileExists('config.yaml')) {
                        error "Файл config.yaml не найден в workspace!"
                    }
                    
                    // Обновляем список кластеров
                    env.CLUSTER_LIST = loadClusterNames().join(',')
                }
            }
        }
        
        stage('Update Parameters') {
            when {
                expression { params.UPDATE_PARAMS }
            }
            steps {
                script {
                    def cluster = getClusterConfig(params.CLUSTER_NAME)
                    if (!cluster) {
                        error "Не могу загрузить конфиг для кластера ${params.CLUSTER_NAME}"
                    }
                    
                    // Обновляем параметры
                    currentBuild.rawBuild.getAction(ParametersAction.class).replace(
                        new StringParameterValue('TARGET_NAME', cluster.target.name),
                        new StringParameterValue('TARGET_BOOTSTRAP', cluster.target.bootstrap)
                    )
                    
                    echo "Параметры успешно обновлены для кластера ${params.CLUSTER_NAME}"
                }
            }
        }
        
        stage('Main Pipeline') {
            steps {
                script {
                    // Проверяем, что параметры загружены
                    if (!params.TARGET_NAME || !params.TARGET_BOOTSTRAP) {
                        error "Параметры кластера не загружены! Запустите с UPDATE_PARAMS=true"
                    }
                    
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