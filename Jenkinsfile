pipeline {
    agent any
    
    stages {
        stage('Preparar Ambiente') {
            steps {
                script {
                    // Parar e remover containers existentes, se houver
                    sh '''
                    docker-compose down || true
                    docker system prune -f || true
                    '''
                }
            }
        }           
                
        stage('Build') {
            steps {
                echo "=== Iniciando build ==="
                sh 'docker-compose build --no-cache'
            }
        }

        stage('Run Test') {
            steps {
                echo "=== Executando testes ==="
                script {
                    // Sobe os serviços necessários para os testes (ex.: banco de dados e aplicação Flask)
                    sh 'docker-compose up -d mariadb flask'
                    sh 'sleep 10' // Tempo para garantir que o banco de dados está pronto
                    // Executa os testes
                    sh 'docker exec $(docker ps -qf "name=flask") pytest'
                    // Derruba os containers após os testes
                    sh 'docker-compose down'
                }
            }
        }
        
        stage('Run Containers') {
            steps {
                echo "=== Iniciando containers ==="
                sh 'docker-compose up -d'
                sh 'sleep 10' // Aguarda containers iniciarem
            }
        }        
  
    }    
    
}