pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
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
