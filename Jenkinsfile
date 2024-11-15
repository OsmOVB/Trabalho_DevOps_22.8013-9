pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        FLASK_APP_URL = 'http://localhost:5000/alunos'
    }

    stages {
        stage('Parar Grafana') {
            steps {
                script {
                    echo "=== Verificando o estado do serviço Grafana ==="
                    
                    // Verifica e para o serviço Grafana diretamente
                    sh '''
                    SERVICE_NAME="grafana-server"

                    if systemctl is-active --quiet $SERVICE_NAME; then
                        echo "O serviço $SERVICE_NAME está em execução. Parando o serviço..."
                        systemctl stop $SERVICE_NAME
                        echo "O serviço $SERVICE_NAME foi parado com sucesso."
                    else
                        echo "O serviço $SERVICE_NAME não está em execução. Nenhuma ação necessária."
                    fi
                    '''
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
                echo "=== Construindo os containers ==="
                sh 'docker-compose build'
            }
        }
        stage('Run Containers') {
            steps {
                echo "=== Iniciando os containers ==="
                sh 'docker-compose up -d'
            }
        }
        stage('Run Tests') {
            steps {
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

    post {
        always {
            echo "=== Derrubando todos os containers ==="
            sh 'docker-compose down'
        }
        success {
            echo "Pipeline executado com sucesso!"
        }
        failure {
            echo "Falha no pipeline. Verifique os logs."
        }
    }
}
