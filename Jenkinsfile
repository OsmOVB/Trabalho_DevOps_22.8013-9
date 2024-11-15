pipeline {
    agent any
    stages {
        stage('Parar Grafana') {
            steps {
                script {
                    echo "=== Verificando o estado do serviço Grafana ==="
                    
                    // Verifica se o serviço está ativo
                    def isGrafanaRunning = sh(
                        script: 'systemctl is-active --quiet grafana-server && echo "running" || echo "stopped"',
                        returnStdout: true
                    ).trim()

                    if (isGrafanaRunning == "running") {
                        echo "Grafana está em execução. Tentando parar o serviço..."
                        
                        // Para o serviço Grafana
                        sh '''
                        sudo systemctl stop grafana-server
                        '''
                        echo "O serviço Grafana foi parado com sucesso."
                    } else {
                        echo "Grafana não está em execução. Nenhuma ação necessária."
                    }
                }
            }
        }
        stage('Checkout') {
            steps {
                echo "=== Iniciando o checkout do repositório ==="
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    echo "=== Construindo os containers ==="
                    sh 'docker-compose build'
                }
            }
        }
        stage('Run Containers') {
            steps {
                script {
                    echo "=== Iniciando os containers ==="
                    sh 'docker-compose up -d'
                }
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    echo "=== Executando os testes ==="

                    // Aguarda até que o Flask esteja disponível
                    sh '''
                    for i in {1..10}; do
                        curl --silent --fail ${FLASK_APP_URL} && break || echo "Aguardando o Flask estar disponível..."
                        sleep 5
                    done
                    '''

                    // Executa o arquivo de teste
                    sh '''
                    python3 -m pip install --no-cache-dir requests
                    python3 test_app.py
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "=== Derrubando todos os containers ==="
                sh 'docker-compose down'
            }
        }
        success {
            echo "Pipeline executado com sucesso!"
        }
        failure {
            echo "Falha no pipeline. Verifique os logs."
        }
    }
}
