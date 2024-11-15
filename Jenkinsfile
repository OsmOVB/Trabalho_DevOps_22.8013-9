pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Pre-Check: Stop Existing Containers') {
            steps {
                // Para o Grafana e Prometheus se já estiverem rodando
                script {
                    echo "=== Verificando e parando containers existentes ==="
                    sh """
                    docker ps -q --filter "name=grafana_project-grafana-1" | grep -q . && docker stop grafana_project-grafana-1 || echo 'Grafana não estava rodando.'
                    docker ps -q --filter "name=grafana_project-prometheus-1" | grep -q . && docker stop grafana_project-prometheus-1 || echo 'Prometheus não estava rodando.'
                    """
                }
            }
        }
        stage('Git Checkout') {
            steps {
                // Usa o repositório já configurado no Jenkins
                echo "=== Iniciando o checkout do repositório Git ==="
                checkout scm
                echo "=== Checkout do Git concluído ==="
            }
        }
        stage('Build') {
            steps {
                // Realiza o build dos containers
                script {
                    echo "=== Iniciando a construção dos containers ==="
                    sh 'docker-compose build'
                    echo "=== Containers construídos com sucesso ==="
                }
            }
        }
        stage('Run Containers') {
            steps {
                // Sobe os containers do docker-compose
                script {
                    echo "=== Iniciando os containers ==="
                    sh 'docker-compose up -d'
                    echo "=== Containers iniciados ==="
                }
            }
        }
    }

    post {
        always {
            // Derruba os containers ao final
            script {
                echo "=== Derrubando todos os containers no final ==="
                sh 'docker-compose down'
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
