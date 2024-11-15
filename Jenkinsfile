pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Pre-Check: Stop and Remove Existing Containers') {
            steps {
                script {
                    echo "=== Verificando e parando containers existentes ==="
                    
                    // Parar e remover todos os containers ativos para evitar conflitos
                    sh """
                    # Lista todos os containers ativos e para cada um deles
                    docker ps -q | xargs -r docker stop || echo 'Nenhum container ativo para parar.'
                    
                    # Remove todos os containers, mesmo os que estão parados
                    docker ps -aq | xargs -r docker rm || echo 'Nenhum container para remover.'
                    """
                    
                    echo "=== Liberando porta 3000 se estiver em uso ==="
                    // Libera a porta 3000 matando qualquer processo que esteja usando-a
                    sh """
                    # Encontra o processo que está usando a porta 3000 e encerra
                    lsof -t -i:3000 | xargs -r kill -9 || echo 'Nenhum processo usando a porta 3000.'
                    """
                }
            }
        }
        stage('Git Checkout') {
            steps {
                echo "=== Iniciando o checkout do repositório Git ==="
                // Realiza o checkout do código fonte a partir do repositório configurado no Jenkins
                checkout scm
                echo "=== Checkout do Git concluído ==="
            }
        }
        stage('Build') {
            steps {
                script {
                    echo "=== Iniciando a construção dos containers ==="
                    // Realiza o build dos containers Docker definidos no docker-compose.yml
                    sh 'docker-compose build'
                    echo "=== Containers construídos com sucesso ==="
                }
            }
        }
        stage('Run Containers') {
            steps {
                script {
                    echo "=== Iniciando os containers ==="
                    // Sobe os serviços do docker-compose
                    sh 'docker-compose up -d'
                    echo "=== Containers iniciados ==="
                }
            }
        }
    }

    post {
        always {
            steps {
                script {
                    echo "=== Derrubando todos os containers no final ==="
                    // Remove todos os containers e redes criados pelo docker-compose
                    sh 'docker-compose down'
                }
            }
        }
        success {
            echo 'Aplicação inicial rodando com sucesso!'
        }
        failure {
            echo 'Falha ao rodar a aplicação. Verifique os logs.'
        }
    }
}
