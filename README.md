# Trabalho Prático - Ciclo 02: Conteinerização de Aplicação

## Alunos

- Caique Moran de Souza - 2312478
- Felipe da Silva Pereira Alves - 2310995
- Laura Beatriz Silva Serbêto - 2321107

## Comandos de Execução (Passo a Passo Manual)

Conforme os requisitos do trabalho, abaixo estão os comandos manuais para criar a rede, fazer o build das imagens e rodar os containers individualmente.

### 1. Criar a rede customizada do projeto
Crie a rede Docker na qual todos os containers irão se comunicar:
```bash
docker network create app-network
```

### 2. Fazer o build das imagens do Frontend e do Backend
A partir da raiz do projeto (`tarefas-docker`), execute o build das duas imagens:
```bash
docker build -t backend-image ./backend
docker build -t frontend-image ./frontend
```

### 3. Rodar o container do Banco de Dados
Inicie o container do PostgreSQL, mapeando a porta padrão e configurando as variáveis de ambiente essenciais (nome do usuário, senha e database):
```bash
docker run -d \
  --name postgres \
  --network app-network \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=tarefasdb \
  -p 5432:5432 \
  postgres:15-alpine
```

### 4. Rodar os containers do Backend e Frontend
Inicie o container do backend (conectando-se ao banco de dados `postgres`) e, por fim, o do frontend, garantindo que ambos estão na rede criada.

**Backend:**
```bash
docker run -d \
  --name backend \
  --network app-network \
  -e DB_HOST=postgres \
  -e DB_PORT=5432 \
  -e DB_USER=postgres \
  -e DB_PASSWORD=postgres \
  -e DB_NAME=tarefasdb \
  -e PORT=3001 \
  -p 3001:3001 \
  backend-image
```

**Frontend:**
```bash
docker run -d \
  --name frontend \
  --network app-network \
  -e VITE_API_URL=http://localhost:3001 \
  -p 5173:5173 \
  frontend-image
```

---

## Alternativa: Execução via Docker Compose

Caso queira executar todos os serviços simultaneamente, também configuramos um `docker-compose.yml` na raiz do projeto. Para rodar, basta usar:

```bash
docker-compose up -d --build
```

Para derrubar os containers e a rede (preservando o volume de banco de dados se não usar a flag `-v`):
```bash
docker-compose down
```