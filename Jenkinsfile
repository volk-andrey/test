properties([
    parameters([
        booleanParam(
            name: 'UPDATE_PARAMS',
            defaultValue: false,
            description: 'Обновить параметры из YAML?'
        ),
        activeChoice(
            name: 'CLUSTER_NAME',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'Выберите кластер',
            script: [
                $class: 'GroovyScript',
                script: [
                    script: '''
                        def config = new groovy.yaml.YamlSlurper().parse(new File("config.yaml"))
                        return config.clusters.keySet() as List
                    ''',
                    sandbox: true
                ],
                fallbackScript: [
                    script: 'return ["Ошибка загрузки кластеров"]',
                    sandbox: true
                ]
            ]
        ),
        activeChoice(
            name: 'TARGET_NAME',
            choiceType: 'PT_READONLY_TEXT',
            description: 'Название целевого кластера',
            script: [
                $class: 'GroovyScript',
                script: [
                    script: '''
                        if (params.CLUSTER_NAME) {
                            def config = new groovy.yaml.YamlSlurper().parse(new File("config.yaml"))
                            return [config.clusters[params.CLUSTER_NAME].target.name]
                        }
                        return [""]
                    ''',
                    sandbox: true
                ]
            ]
        ),
        activeChoice(
            name: 'TARGET_BOOTSTRAP',
            choiceType: 'PT_READONLY_TEXT',
            description: 'Bootstrap целевого кластера',
            script: [
                $class: 'GroovyScript',
                script: [
                    script: '''
                        if (params.CLUSTER_NAME) {
                            def config = new groovy.yaml.YamlSlurper().parse(new File("config.yaml"))
                            return [config.clusters[params.CLUSTER_NAME].target.bootstrap]
                        }
                        return [""]
                    ''',
                    sandbox: true
                ]
            ]
        )
    ])
])

pipeline {
    agent any
    stages {
        stage('Main Pipeline') {
            steps {
                script {
                    echo """
                    Конфигурация:
                    - Кластер: ${params.CLUSTER_NAME}
                    - Target: ${params.TARGET_NAME} (${params.TARGET_BOOTSTRAP})
                    """
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