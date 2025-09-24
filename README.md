# Projeto de Engenharia de Dados: Análise do Dataset Olist

Este projeto implementa um pipeline de dados completo para processar e analisar o dataset de e-commerce da Olist. Utilizando a **Arquitetura Medalhão (Bronze, Silver e Gold)**, o pipeline transforma dados brutos em insights de negócio acionáveis, garantindo qualidade e rastreabilidade em cada etapa.

## 🛠️ Stack Tecnológica

* **Linguagem:** Python 3.10
* **Bibliotecas:** Pandas, Numpy, SQLAlchemy, Psycopg2
* **Banco de Dados:** PostgreSQL 13
* **Ambiente e Orquestração:** Docker e Docker Compose
* **Desenvolvimento:** Jupyter Notebook

---

## 📋 Pré-requisitos

Antes de iniciar, certifique-se de que os seguintes softwares estão instalados e configurados em seu sistema:

1.  **Git:** Essencial para clonar o repositório.
2.  **Docker e Docker Compose:** Para executar o ambiente do banco de dados de forma isolada e consistente.
    * **Link de Instalação:** [Instale o Docker Desktop](https://www.docker.com/products/docker-desktop/)
3.  **Python 3.10:** A versão da linguagem utilizada no projeto.
    * Verifique sua versão com o comando: `python --version`

---

## 🚀 Guia de Instalação e Execução

Siga os passos detalhados abaixo para configurar e executar o projeto em sua máquina local.

### 1. Clonar o Repositório

Primeiro, clone o repositório do projeto para o seu ambiente de trabalho.

```bash
git clone [https://github.com/seu-usuario/seu-repositorio.git](https://github.com/seu-usuario/seu-repositorio.git)
cd seu-repositorio
```

### 2. Configurar o Banco de Dados (PostgreSQL com Docker)

O banco de dados PostgreSQL será executado em um contêiner Docker gerenciado pelo Docker Compose.

**a. Altere as credenciais do banco:**

Por segurança, é crucial que você **altere as credenciais padrão**. Abra o arquivo `docker-compose.yml` e modifique as variáveis de ambiente:

```yaml
version: '3.8'

services:
  db:
    image: postgres:13
    container_name: postgres_db
    restart: always
    environment:
      POSTGRES_USER: altere_para_seu_usuario      # <-- ALTERE AQUI
      POSTGRES_PASSWORD: altere_para_sua_senha    # <-- ALTERE AQUI
      POSTGRES_DB: olist_db                       # <-- (Opcional) Altere o nome do banco
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

**b. Inicie o contêiner:**

Com o Docker em execução, navegue até a raiz do projeto e execute o comando:

```bash
docker-compose up -d
```
O parâmetro `-d` executa o contêiner em segundo plano. Para verificar se ele está ativo, use `docker ps`.

### 3. Configurar o Ambiente Python

**a. Crie e ative uma Virtual Environment (`venv`):**

Isso mantém as dependências do projeto isoladas do restante do sistema.

```bash
# Crie a venv
python -m venv venv

# Ative a venv
# No Windows:
venv\Scripts\activate
# No macOS/Linux:
source venv/bin/activate
```

**b. Instale as dependências:**

Crie um arquivo `requirements.txt` na raiz do projeto com o seguinte conteúdo:

```txt
pandas
numpy
sqlalchemy
psycopg2-binary
jupyter
```
Em seguida, instale todas as bibliotecas de uma vez:
```bash
pip install -r requirements.txt
```

### 4. Baixar o Dataset da Olist

**a.** Faça o download do dataset no site do Kaggle:
* [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

**b.** Descompacte o arquivo `.zip` e mova todos os arquivos `.csv` para dentro da pasta `data/raw` na raiz do projeto. (Crie esta estrutura de pastas se ela não existir).

### 5. Configurar a Conexão nos Scripts Python

Seus scripts precisam saber como se conectar ao banco de dados. Abra cada notebook ou script Python e localize a string de conexão da **SQLAlchemy**. Atualize-a com **as mesmas credenciais que você definiu no arquivo `docker-compose.yml`**.

```python
from sqlalchemy import create_engine

# ATENÇÃO: Atualize esta linha com suas credenciais!
# Formato: "postgresql://USUARIO:SENHA@HOST:PORTA/NOME_DO_BANCO"
DATABASE_URL = "postgresql://altere_para_seu_usuario:altere_para_sua_senha@localhost:5432/olist_db"

engine = create_engine(DATABASE_URL)
```

### 6. Executar o Pipeline de Dados

Com o ambiente configurado, execute o pipeline para processar os dados.

**a. Inicie o Jupyter Notebook:**
```bash
jupyter notebook
```

**b. Execute os Notebooks na ordem correta:**

No seu navegador, abra e execute as células dos notebooks na sequência da Arquitetura Medalhão:

1.  **`1_bronze_ingestion.ipynb`**: Carrega os dados brutos dos arquivos CSV para o schema `bronze` no PostgreSQL.
2.  **`2_silver_processing.ipynb`**: Lê os dados da camada Bronze, aplica limpezas, transformações e salva as tabelas tratadas no schema `silver`.
3.  **`3_gold_aggregation.ipynb`**: Utiliza os dados da camada Silver para criar tabelas de negócio agregadas no schema `gold`, prontas para análise.

### 7. Verificação dos Resultados

Após a execução, você pode usar uma ferramenta de cliente SQL (DBeaver, pgAdmin, etc.) para se conectar ao banco de dados na porta `5432` e verificar se os schemas (`bronze`, `silver`, `gold`) e suas respectivas tabelas foram criados com sucesso.

### 8. Parando o Ambiente

Quando terminar, pare e remova o contêiner do Docker com o seguinte comando. Seus dados serão preservados no volume `postgres_data`.

```bash
docker-compose down
```