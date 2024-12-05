# grafana-project

# Osmar Vieira Borges
# RA: 228013-9 

# Configuração do Ambiente DevOps

## Visão Geral
Este repositório contém os arquivos e configurações necessários para implementar um ambiente completo de DevOps, incluindo:
- Uma aplicação web em Flask com banco de dados MariaDB.
- Pipeline de CI/CD no Jenkins.
- Monitoramento com Prometheus e Grafana.
- Provisionamento via Docker.

---

## Instruções de Configuração

### 1. Pré-requisitos
Certifique-se de que as seguintes ferramentas estão instaladas:
- **Docker** e **Docker Compose**
- **Jenkins**
- **Git**
- **Python** (com Flask e bibliotecas relacionadas)
- Navegador para acessar **Prometheus** e **Grafana**

---


2. **Configuração inicial**:
   - Acesse o Jenkins em `http://localhost:8080`.
   - Configure o administrador e instale os plugins sugeridos.

3. **Instalar plugins adicionais**:
   - No painel do Jenkins, instale:
     - **Pipeline**
     - **Docker Pipeline**  
     - **Git**

4. **Criação da Pipeline**:
   - Configure um novo projeto de pipeline.
   - Utilize o arquivo `Jenkinsfile` deste repositório como definição das etapas.

---

### 2. Configuração do Ambiente Local

#### Rodando a aplicação
O ambiente completo é provisionado via Docker Compose.

1. **Executar o script de inicialização**:
   - No diretório raiz do projeto, execute:
     ```bash
     sh start.sh     
     ```

2. **Verificação do ambiente**:
   ## Acessos
- Flask: [http://localhost:5000](http://localhost:5000)
- Grafana: [http://localhost:3000](http://localhost:3000)
- Prometheus: [http://localhost:9090](http://localhost:9090)
- Jenkins: [http://localhost:8080](http://localhost:8080)

---

### 3. Configuração do Prometheus e Grafana

#### Prometheus
- Arquivo de configuração: `prometheus.yml`.
- Certifique-se de que as métricas da aplicação Flask e do banco de dados MariaDB estão sendo coletadas.

### 4. Testes

- Testes unitários são executados automaticamente pela pipeline no Jenkins.
- Para rodar manualmente:
  ```bash
  pytest tests/test_app.py
