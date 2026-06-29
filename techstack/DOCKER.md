# 🐳 Docker — Learning Guide (Beginner → Advanced)

## Tổng quan

Docker là containerization platform. Đóng gói ứng dụng + dependencies vào container chạy consistent trên mọi môi trường. Trong data: chạy Airflow, dbt, OpenMetadata locally.

**Dùng cho:** AE, BI Eng, DE  
**Use cases:** Local development, CI/CD, deployment

---

## Level 1: Beginner (1 tuần)

### Core Concepts
- **Image**: blueprint (read-only template)
- **Container**: running instance of image
- **Dockerfile**: instructions to build image
- **Registry**: store images (Docker Hub, ECR)
- **Volume**: persistent data storage
- **Network**: container communication

### Essential Commands
```bash
# Images
docker pull python:3.11-slim       # Download image
docker images                       # List local images
docker rmi image_name               # Remove image

# Containers
docker run -it python:3.11-slim bash  # Run interactive
docker run -d -p 8080:80 nginx        # Run detached, port mapping
docker ps                             # List running containers
docker ps -a                          # List all (including stopped)
docker stop container_id              # Stop container
docker rm container_id                # Remove container
docker logs container_id              # View logs
docker exec -it container_id bash     # Shell into running container

# Cleanup
docker system prune                   # Remove unused resources
```

### First Dockerfile
```dockerfile
# Dockerfile for Python data script
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "main.py"]
```

```bash
# Build and run
docker build -t my-data-app .
docker run my-data-app
```

### Bài tập
1. Pull and run Python container interactively
2. Write Dockerfile for simple Python script
3. Build image, run container
4. Use `-v` to mount local folder into container
5. Run PostgreSQL container with persistent volume

### Resources
| Resource | Link | Type |
|---|---|---|
| Docker Get Started | [docs.docker.com/get-started](https://docs.docker.com/get-started/) | Free |
| Play with Docker | [labs.play-with-docker.com](https://labs.play-with-docker.com/) | Free |
| Docker Hub | [hub.docker.com](https://hub.docker.com/) | Free |

---

## Level 2: Intermediate (2 tuần)

### Docker Compose (multi-container)
```yaml
# docker-compose.yml — Airflow local dev
version: '3.8'
services:
  postgres:
    image: postgres:14
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
    volumes:
      - postgres_data:/var/lib/postgresql/data

  airflow-webserver:
    image: apache/airflow:2.8.0
    depends_on:
      - postgres
    environment:
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    ports:
      - "8080:8080"
    volumes:
      - ./dags:/opt/airflow/dags

  airflow-scheduler:
    image: apache/airflow:2.8.0
    depends_on:
      - postgres
    environment:
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    volumes:
      - ./dags:/opt/airflow/dags

volumes:
  postgres_data:
```

```bash
docker compose up -d          # Start all services
docker compose ps             # Status
docker compose logs -f        # Follow logs
docker compose down           # Stop + remove
docker compose down -v        # Stop + remove + volumes
```

### Multi-stage Builds
```dockerfile
# Stage 1: Build
FROM python:3.11 AS builder
WORKDIR /build
COPY requirements.txt .
RUN pip install --prefix=/install -r requirements.txt

# Stage 2: Runtime (smaller image)
FROM python:3.11-slim
COPY --from=builder /install /usr/local
COPY . /app
WORKDIR /app
CMD ["python", "main.py"]
```

### Networking
```bash
# Create network
docker network create data-network

# Run containers on same network
docker run -d --network data-network --name db postgres:14
docker run -d --network data-network --name app my-app
# app can reach db via hostname "db"
```

### Bài tập
1. Create docker-compose for Airflow (webserver + scheduler + DB)
2. Multi-stage build for dbt project
3. Set up local OpenMetadata with Docker
4. Network: Python app connecting to PostgreSQL container
5. Volume mounts: persist dbt target/ between runs

### Resources
| Resource | Link | Type |
|---|---|---|
| Docker Compose docs | [docs.docker.com/compose](https://docs.docker.com/compose/) | Free |
| Airflow Docker Compose | [airflow.apache.org/docs/docker-compose](https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html) | Free |

---

## Level 3: Advanced (3 tuần)

### CI/CD with Docker
```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - docker build -t registry.company.cloud/dbt-app:$CI_COMMIT_SHA .
    - docker push registry.company.cloud/dbt-app:$CI_COMMIT_SHA

test:
  stage: test
  image: registry.company.cloud/dbt-app:$CI_COMMIT_SHA
  script:
    - dbt test --target dev

deploy:
  stage: deploy
  script:
    - docker tag registry.company.cloud/dbt-app:$CI_COMMIT_SHA registry.company.cloud/dbt-app:latest
    - docker push registry.company.cloud/dbt-app:latest
```

### Docker for Development
```dockerfile
# Dev container for dbt
FROM python:3.11-slim

RUN pip install dbt-redshift==1.10.1 sqlfluff

WORKDIR /dbt
VOLUME /dbt

ENTRYPOINT ["dbt"]
```

```bash
# Run dbt commands via Docker
docker run -v $(pwd):/dbt -e DBT_PROFILES_DIR=/dbt/profiles dbt-dev run -s my_model
```

### Security Best Practices
- Use non-root user in Dockerfile
- Scan images for vulnerabilities (Trivy, Snyk)
- Use `.dockerignore` (exclude .env, .git, venv)
- Pin image versions (not `:latest` in production)
- Use secrets (not ENV for passwords)

### Bài tập
1. Create CI/CD pipeline with Docker build + push
2. Build dev container for dbt project
3. Security scan existing Dockerfiles
4. Optimize image size (multi-stage, slim base, .dockerignore)
5. Set up local data stack: Postgres + Airflow + dbt + Metabase

### Resources
| Resource | Link | Type |
|---|---|---|
| Docker Security Best Practices | [docs.docker.com/security](https://docs.docker.com/develop/security-best-practices/) | Free |
| Dive (analyze image layers) | [github.com/wagoodman/dive](https://github.com/wagoodman/dive) | Free |
| Docker for Data Engineering | DataTalks.Club Zoomcamp | Free |

---
## Alternatives & Công cụ tương đương

| Tool | Description | vs Docker |
|---|---|---|
| **Podman** | Daemonless, rootless containers | Drop-in Docker replacement, more secure |
| **containerd** | Container runtime (used by Docker internally) | Lower-level, Kubernetes native |
| **Kubernetes (K8s)** | Container orchestration | Production-scale, complex |
| **Docker Compose** | Multi-container local | Development environments |
| **Helm** | K8s package manager | Deploy to Kubernetes |
| **Nix** | Reproducible builds | Alternative to Docker for dev envs |
| **DevContainers** | VS Code dev environments | IDE-integrated containers |

### Khi nào chọn gì?
- **Docker + Compose** → Local dev, CI/CD, small deployments
- **Kubernetes** → Production, multi-service, scaling
- **Podman** → Security-conscious, rootless needed
- **DevContainers** → Team standardization of dev env
