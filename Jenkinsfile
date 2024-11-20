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
                    def portInUse = sh(script: 'lsof -t -i:3000', returnStdout: true).trim()
                    if (portInUse) {
                        echo "Porta 3000 em uso pelo processo: ${portInUse}"
                        sh "kill -9 ${portInUse}"
                        echo "Processo ${portInUse} encerrado"
                    } else {
                        echo "Porta 3000 está livre"
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