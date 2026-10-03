# Docker Commands — Beginner to Advanced

A practical Docker command reference for a fresher, with examples for FastAPI, PostgreSQL, Redis, and Docker Compose.

---

## 1. Docker Installation & Information

### Check Docker Version

```bash
docker --version
```

### Check Docker Information

```bash
docker info
```

### Docker Help

```bash
docker --help
```

### Help for a Specific Command

```bash
docker run --help
```

---

# 2. Docker Images

### Pull an Image

```bash
docker pull redis
```

```bash
docker pull redis:7-alpine
```

### List Images

```bash
docker images
```

```bash
docker image ls
```

### Remove an Image

```bash
docker rmi redis
```

### Force Remove an Image

```bash
docker rmi -f redis
```

### Remove Unused Images

```bash
docker image prune
```

---

# 3. Docker Containers

### Run a Container

```bash
docker run redis
```

### Run in Background

```bash
docker run -d redis
```

### Give a Container a Name

```bash
docker run -d --name myredis redis
```

### Map Ports

```bash
docker run -d --name myredis -p 6379:6379 redis
```

Format:

```text
-p HOST_PORT:CONTAINER_PORT
```

Example:

```bash
docker run -d -p 8000:8000 myapp
```

### List Running Containers

```bash
docker ps
```

### List All Containers

```bash
docker ps -a
```

### Stop a Container

```bash
docker stop myredis
```

### Start a Stopped Container

```bash
docker start myredis
```

### Restart a Container

```bash
docker restart myredis
```

### Remove a Container

```bash
docker rm myredis
```

### Force Remove a Container

```bash
docker rm -f myredis
```

---

# 4. Docker Logs

### View Logs

```bash
docker logs myredis
```

### Follow Logs

```bash
docker logs -f myredis
```

### Show Last 100 Lines

```bash
docker logs --tail 100 myredis
```

---

# 5. Execute Commands Inside Containers

### Open Bash Shell

```bash
docker exec -it myredis bash
```

### Open Shell

If `bash` is not available:

```bash
docker exec -it myredis sh
```

### Execute Redis CLI

```bash
docker exec -it myredis redis-cli
```

### Exit Container Shell

```bash
exit
```

---

# 6. Inspect Containers

### Inspect Container

```bash
docker inspect myredis
```

### View Container Processes

```bash
docker top myredis
```

### View Resource Usage

```bash
docker stats
```

### View Resource Usage for One Container

```bash
docker stats myredis
```

---

# 7. Copy Files

### Copy File from Host to Container

```bash
docker cp file.txt myredis:/app/file.txt
```

### Copy File from Container to Host

```bash
docker cp myredis:/app/file.txt .
```

---

# 8. Environment Variables

### Set an Environment Variable

```bash
docker run -d \
  --name myapp \
  -e DATABASE_URL="postgresql://user:password@db:5432/app" \
  myapp:latest
```

### Set Multiple Environment Variables

```bash
docker run -d \
  --name myapp \
  -e DEBUG=false \
  -e SECRET_KEY=abc123 \
  myapp
```

### Use an `.env` File

```bash
docker run --env-file .env myapp
```

---

# 9. Docker Volumes

Volumes are used to persist data outside the container.

### Create a Volume

```bash
docker volume create postgres_data
```

### List Volumes

```bash
docker volume ls
```

### Inspect a Volume

```bash
docker volume inspect postgres_data
```

### Use a Volume

```bash
docker run -d \
  --name postgres \
  -v postgres_data:/var/lib/postgresql/data \
  postgres
```

### Remove a Volume

```bash
docker volume rm postgres_data
```

### Remove Unused Volumes

```bash
docker volume prune
```

---

# 10. Docker Networks

Networks allow containers to communicate with each other.

### List Networks

```bash
docker network ls
```

### Create a Network

```bash
docker network create diagnostic-network
```

### Run a Container on a Network

```bash
docker run -d \
  --name redis \
  --network diagnostic-network \
  redis:7-alpine
```

### Run Another Container on the Same Network

```bash
docker run -d \
  --name backend \
  --network diagnostic-network \
  my-backend
```

Inside the Docker network, the backend can connect to Redis using:

```text
redis://redis:6379
```

Do not use `localhost` for container-to-container communication.

---

# 11. Dockerfile

A Dockerfile contains instructions for building a Docker image.

### Example FastAPI Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app ./app

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

# 12. Important Dockerfile Instructions

| Instruction | Purpose |
|---|---|
| `FROM` | Selects the base image |
| `WORKDIR` | Sets the working directory |
| `COPY` | Copies files into the image |
| `RUN` | Runs a command while building the image |
| `ENV` | Sets environment variables |
| `EXPOSE` | Documents the container port |
| `CMD` | Sets the default command |
| `ENTRYPOINT` | Defines the main executable |

---

# 13. Build Docker Images

### Build an Image

```bash
docker build -t diagnostic-backend .
```

### List Images

```bash
docker images
```

### Run the Built Image

```bash
docker run -p 8000:8000 diagnostic-backend
```

### Build with a Version Tag

```bash
docker build -t diagnostic-backend:1.0 .
```

---

# 14. `.dockerignore`

Create a `.dockerignore` file to avoid copying unnecessary files.

Example:

```text
venv/
__pycache__/
*.pyc
.git/
.env
node_modules/
.pytest_cache/
```

---

# 15. FastAPI + Docker Practice

## Project Structure

```text
fastapi-docker/
├── app/
│   └── main.py
├── requirements.txt
├── Dockerfile
└── .dockerignore
```

## `app/main.py`

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def home():
    return {"message": "FastAPI is running in Docker!"}


@app.get("/hello")
def hello():
    return {"message": "Hello from Docker"}
```

## `requirements.txt`

```text
fastapi
uvicorn
```

## Build

```bash
docker build -t fastapi-demo .
```

## Run

```bash
docker run -d --name fastapi-container -p 8000:8000 fastapi-demo
```

## Check Container

```bash
docker ps
```

## View Logs

```bash
docker logs fastapi-container
```

## Test FastAPI

Browser:

```text
http://localhost:8000
```

Swagger:

```text
http://localhost:8000/docs
```

Test endpoint:

```text
GET http://localhost:8000/hello
```

Expected response:

```json
{
  "message": "Hello from Docker"
}
```

## Enter the FastAPI Container

```bash
docker exec -it fastapi-container bash
```

If Bash is unavailable:

```bash
docker exec -it fastapi-container sh
```

## Stop FastAPI Container

```bash
docker stop fastapi-container
```

## Remove FastAPI Container

```bash
docker rm fastapi-container
```

---

# 16. Docker Compose

Docker Compose is useful when your application has multiple services.

For example:

```text
Frontend
Backend
PostgreSQL
Redis
```

### Start Services

```bash
docker compose up
```

### Start in Background

```bash
docker compose up -d
```

### Build and Start

```bash
docker compose up --build
```

### Build and Start in Background

```bash
docker compose up -d --build
```

### Stop and Remove Services

```bash
docker compose down
```

### List Services

```bash
docker compose ps
```

### View All Logs

```bash
docker compose logs
```

### Follow All Logs

```bash
docker compose logs -f
```

### View Specific Service Logs

```bash
docker compose logs backend
```

### Follow Specific Service Logs

```bash
docker compose logs -f backend
```

### Restart a Service

```bash
docker compose restart backend
```

### Build Services

```bash
docker compose build
```

### Pull Images

```bash
docker compose pull
```

---

# 17. Docker Compose Example — FastAPI + PostgreSQL + Redis

Example `docker-compose.yml`:

```yaml
services:

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:16
    environment:
      POSTGRES_DB: diagnostic_ai
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

volumes:
  postgres_data:
```

### Start Everything

```bash
docker compose up -d
```

### Check Services

```bash
docker compose ps
```

### View Backend Logs

```bash
docker compose logs -f backend
```

### Stop Everything

```bash
docker compose down
```

---

# 18. PostgreSQL with Docker

### Pull PostgreSQL

```bash
docker pull postgres:16
```

### Run PostgreSQL

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=diagnostic_ai \
  -p 5432:5432 \
  postgres:16
```

### Check PostgreSQL Container

```bash
docker ps
```

### View PostgreSQL Logs

```bash
docker logs postgres
```

---

# 19. Redis with Docker

### Pull Redis

```bash
docker pull redis:7-alpine
```

### Run Redis

```bash
docker run -d \
  --name diagnostic-ai-redis \
  -p 6379:6379 \
  redis:7-alpine
```

### Check Redis

```bash
docker ps
```

### Open Redis CLI

```bash
docker exec -it diagnostic-ai-redis redis-cli
```

### Test Redis

```text
PING
```

Expected:

```text
PONG
```

---

# 20. Docker Cleanup

### Docker Disk Usage

```bash
docker system df
```

### Remove Stopped Containers

```bash
docker container prune
```

### Remove Unused Images

```bash
docker image prune
```

### Remove Unused Networks

```bash
docker network prune
```

### Remove Unused Volumes

```bash
docker volume prune
```

### Remove Unused Docker Resources

```bash
docker system prune
```

### Remove All Unused Images Too

```bash
docker system prune -a
```

Be careful with `-a` because unused images will also be removed.

---

# 21. Docker Tags

### Tag an Image

```bash
docker tag diagnostic-backend diagnostic-backend:v1
```

### Build with a Tag

```bash
docker build -t diagnostic-backend:1.0 .
```

### Run a Specific Version

```bash
docker run diagnostic-backend:1.0
```

---

# 22. Docker Registry / Docker Hub

### Login

```bash
docker login
```

### Tag an Image for Docker Hub

```bash
docker tag diagnostic-backend username/diagnostic-backend:1.0
```

### Push Image

```bash
docker push username/diagnostic-backend:1.0
```

### Pull Image

```bash
docker pull username/diagnostic-backend:1.0
```

---

# 23. Health Checks

Example in a Dockerfile:

```dockerfile
HEALTHCHECK CMD curl --fail http://localhost:8000/health || exit 1
```

Then check:

```bash
docker ps
```

The container can show a health status such as:

```text
healthy
```

---

# 24. Resource Limits

### Limit Memory

```bash
docker run -d --memory="512m" myapp
```

### Limit CPU

```bash
docker run -d --cpus="1.0" myapp
```

---

# 25. Multi-Stage Builds

Multi-stage builds can reduce production image size.

Example:

```dockerfile
FROM node:20 AS builder

WORKDIR /app

COPY package*.json .

RUN npm install

COPY . .

RUN npm run build


FROM node:20-alpine

WORKDIR /app

COPY --from=builder /app ./

CMD ["npm", "start"]
```

---

# 26. Most Important Commands to Memorize

## Beginner

```bash
docker --version
docker pull
docker images
docker run
docker ps
docker ps -a
docker stop
docker start
docker restart
docker rm
docker rmi
docker logs
docker exec
```

## Intermediate

```bash
docker build
docker tag
docker cp
docker inspect
docker stats
docker volume
docker network
docker compose up
docker compose down
docker compose ps
docker compose logs
docker compose restart
```

## Advanced

```bash
docker system df
docker system prune
docker compose build
docker compose up -d --build
docker login
docker push
docker pull
```

---

# 27. Docker Learning Order

Study Docker in this order:

```text
1. Docker Concepts
       ↓
2. Images vs Containers
       ↓
3. Basic Docker Commands
       ↓
4. Dockerfile
       ↓
5. Build & Run Images
       ↓
6. Ports
       ↓
7. Environment Variables
       ↓
8. Volumes
       ↓
9. Networks
       ↓
10. Docker Compose
       ↓
11. Docker + FastAPI
       ↓
12. PostgreSQL + Docker
       ↓
13. Redis + Docker
       ↓
14. Debugging with Logs
       ↓
15. Docker Hub
       ↓
16. Health Checks
       ↓
17. Multi-stage Builds
       ↓
18. Production & CI/CD
```

---

# 28. Docker Flow to Remember

## Single Application

```text
Dockerfile
    ↓
docker build
    ↓
Docker Image
    ↓
docker run
    ↓
Container
    ↓
docker logs / docker exec / docker inspect
```

## Multi-Service Application

```text
docker-compose.yml
        ↓
docker compose up
        ↓
┌─────────────────────────┐
│        Frontend         │
│         Backend         │
│       PostgreSQL        │
│          Redis          │
└─────────────────────────┘
```

---

# 29. Diagnostic AI Docker Practice

For your Diagnostic AI project, aim to understand and run:

```text
Next.js
   ↓
FastAPI
   ↓
PostgreSQL
   ↓
Redis
```

Using:

```bash
docker compose up -d
```

Then learn to troubleshoot with:

```bash
docker compose ps
docker compose logs -f
docker compose logs -f backend
docker exec -it <container> bash
docker stats
docker inspect <container>
```

These commands and concepts are the most relevant for working with Docker as a Python/FastAPI backend developer.
