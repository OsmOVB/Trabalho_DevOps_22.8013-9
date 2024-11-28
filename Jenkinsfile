pipeline {
    agent any
    
    stages {
        stage('Preparar Ambiente') {
            steps {
                script {
                    // Parar e remover containers existentes, se houver
                    sh '''
                    docker ps -aq | xargs -r docker stop
                    docker ps -aq | xargs -r docker rm
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

        stage('Testar Aplicação') {
            steps {
                echo "=== Executando testes ==="
                script {
                    // Subir os serviços
                    sh 'docker-compose up -d mariadb flask'
                    sh 'sleep 10' // Aguarda inicialização dos serviços

                    // Rodar os testes dentro do container Flask
                    sh 'docker exec $(docker ps -qf "name=flask") python /app/tests/test_cadastrar_aluno.py'

                    // Derrubar os serviços após os testes
                    sh 'docker-compose down'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker-compose build'  // Construindo as imagens Docker
            }
        }
        stage('Deploy Application') {
            steps {
                sh 'docker-compose up -d'  // Subindo os containers
            }
        }
       post {
        always {
            echo 'Pipeline executada com sucesso!'
            echo 'grafana rodando em http://localhost:3000'
            echo 'Prometheus rodando em http://localhost:9090'
            echo 'Flask rodando em http://localhost:5000'
            echo 'Lista de alunos em http://localhost:5000/alunos' 
        }
    }  
  
    }    
    
}