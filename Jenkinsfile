pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        FLASK_APP_URL = 'http://localhost:5000/alunos'
        GRAFANA_PORT = '3000'
    }

    stages {
        stage('Stop Conflicting Containers') {
            steps {
                script {
                    echo "=== Parando containers existentes, se houver ==="

                    // Para todos os containers e remove aqueles que possam estar usando a porta 3000
                    sh '''
                    # Verifica se há containers usando a porta 3000
                    CONTAINER_ID=$(docker ps -q --filter "publish=${GRAFANA_PORT}")

                    if [ ! -z "$CONTAINER_ID" ]; then
                        echo "Container em execução na porta ${GRAFANA_PORT}. Derrubando..."
                        docker stop $CONTAINER_ID && docker rm $CONTAINER_ID
                    else
                        echo "Nenhum container em execução na porta ${GRAFANA_PORT}."
                    fi

                    # Derruba todos os containers existentes no projeto
                    docker-compose down || true
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
