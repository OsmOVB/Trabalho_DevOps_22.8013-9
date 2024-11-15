pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        FLASK_APP_URL = 'http://localhost:5000/alunos'
    }

    stages {
        stage('Stop Existing Environment') {
            steps {
                script {
                    echo "=== Derrubando containers existentes, se houver ==="
                    
                    // Garante que nenhum container antigo cause conflito
                    sh '''
                    if [ -f ${DOCKER_COMPOSE_FILE} ]; then
                        echo "Arquivo docker-compose encontrado. Parando containers..."
                        docker-compose down || true
                    else
                        echo "Arquivo docker-compose não encontrado. Nenhuma ação necessária."
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
