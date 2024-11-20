pipeline {
    agent any
    
    stages {
        stage('Verificar Ambiente') {
            steps {
                sh '''
                    echo "=== Verificando permissões ==="
                    if ! groups jenkins | grep -q docker; then
                        echo "ERRO: usuário jenkins precisa estar no grupo docker"
                        exit 1
                    fi
                '''
            }
        }
        
        stage('Free Port 3000') {
            steps {
                script {
                    echo "=== Garantindo que a porta 3000 está livre ==="
                    def portInUse = sh(script: 'lsof -t -i:3000', returnStatus: true)
                    if (portInUse == 0) {
                        sh 'kill $(lsof -t -i:3000)'
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                echo "=== Iniciando build ==="
                sh 'docker-compose build --no-cache'
            }
        }
        
        stage('Run Containers') {
            steps {
                echo "=== Iniciando containers ==="
                sh 'docker-compose up -d'
                sh 'sleep 10' // Aguarda containers iniciarem
            }
        }
        
        stage('Run Tests') {
            steps {
                echo "=== Executando testes ==="
                sh 'curl -f http://localhost:3000 || exit 1'
            }
        }
    }
    
    post {
        always {
            echo "=== Limpando ambiente ==="
            sh 'docker-compose down'
        }
        failure {
            echo "Pipeline falhou. Verificar logs acima para detalhes."
        }
    }
}