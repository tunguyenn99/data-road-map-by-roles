# 🧙 Mage — Learning Guide (Beginner → Advanced)

## Tổng quan

**Mage** là một modern data pipeline tool với giao diện notebook-like, cho phép bạn build, run, và monitor data pipelines trực quan. Mage kết hợp ưu điểm của notebook (interactive development) với production-grade orchestration.

**Use cases:**
- ETL/ELT pipelines với visual UI
- Real-time streaming pipelines
- ML feature engineering
- dbt orchestration

**Ai dùng:** Data Engineers, Analytics Engineers, Data Scientists muốn pipeline tool dễ tiếp cận

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Block** | Unit nhỏ nhất — một đoạn code thực hiện 1 việc |
| **Data Loader** | Block load data từ source (API, DB, file) |
| **Transformer** | Block transform data (clean, enrich, aggregate) |
| **Data Exporter** | Block ghi data ra destination |
| **Pipeline** | Chuỗi blocks kết nối với nhau |
| **Trigger** | Cách khởi chạy pipeline (schedule, event, API) |

### Setup

```bash
# Install
pip install mage-ai

# Start Mage (creates project if not exists)
mage start my_project

# Access UI at http://localhost:6789
```

```bash
# Docker alternative
docker run -it -p 6789:6789 -v $(pwd):/home/src mageai/mageai \
  mage start my_project
```

### First Pipeline — 3 Block Types

**Data Loader (load_api_data.py):**
```python
import requests
import pandas as pd

if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader

@data_loader
def load_data(*args, **kwargs) -> pd.DataFrame:
    """Load data from a public API."""
    url = "https://jsonplaceholder.typicode.com/users"
    response = requests.get(url)
    return pd.DataFrame(response.json())
```

**Transformer (clean_users.py):**
```python
import pandas as pd

if 'transformer' not in globals():
    from mage_ai.data_preparation.decorators import transformer

@transformer
def transform(data: pd.DataFrame, *args, **kwargs) -> pd.DataFrame:
    """Clean and transform user data."""
    # Select relevant columns
    df = data[["id", "name", "email", "company"]].copy()
    
    # Extract company name from nested dict
    df["company_name"] = df["company"].apply(lambda x: x["name"])
    df = df.drop(columns=["company"])
    
    # Normalize email
    df["email"] = df["email"].str.lower()
    
    return df
```

**Data Exporter (export_to_csv.py):**
```python
import pandas as pd

if 'data_exporter' not in globals():
    from mage_ai.data_preparation.decorators import data_exporter

@data_exporter
def export_data(data: pd.DataFrame, *args, **kwargs) -> None:
    """Export data to CSV file."""
    data.to_csv("output/cleaned_users.csv", index=False)
    print(f"Exported {len(data)} rows")
```

### Bài tập

1. **Install & Explore** — Cài Mage, mở UI, explore các templates có sẵn
2. **3-block pipeline** — Tạo pipeline: load từ API → transform → export CSV
3. **Schedule** — Tạo trigger chạy pipeline mỗi giờ
4. **Database pipeline** — Load từ PostgreSQL → transform → export về PostgreSQL table khác

### Resources

- 📖 [Mage Docs](https://docs.mage.ai/)
- 📖 [Mage Quickstart](https://docs.mage.ai/getting-started/setup)
- 💻 [Mage GitHub](https://github.com/mage-ai/mage-ai)
- 🎬 [Mage YouTube](https://www.youtube.com/@maboroshi_mage)

---

## Level 2: Intermediate (2–4 tuần)

### Templates & Reusable Blocks

```python
# Custom block template
# templates/custom_loader.py
if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader

@data_loader
def load_from_s3(bucket: str, key: str, *args, **kwargs) -> pd.DataFrame:
    """Reusable S3 loader template."""
    import boto3
    
    s3 = boto3.client("s3")
    obj = s3.get_object(Bucket=bucket, Key=key)
    return pd.read_parquet(obj["Body"])
```

### dbt Integration

```yaml
# mage_project/dbt/profiles.yml
my_dbt_project:
  target: dev
  outputs:
    dev:
      type: postgres
      host: "{{ env_var('DB_HOST') }}"
      port: 5432
      user: "{{ env_var('DB_USER') }}"
      password: "{{ env_var('DB_PASSWORD') }}"
      dbname: analytics
      schema: public
```

```python
# dbt block in Mage
if 'transformer' not in globals():
    from mage_ai.data_preparation.decorators import transformer

@transformer
def run_dbt_model(*args, **kwargs):
    """Run dbt models within Mage pipeline."""
    from mage_ai.data_preparation.models.block.dbt.utils import run_dbt
    
    return run_dbt(
        command="run",
        project_path="dbt/my_project",
        profiles_path="dbt/profiles.yml",
        select="staging.stg_customers",
    )
```

### Streaming Pipelines

```python
# Streaming data loader (Kafka consumer)
if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader

@data_loader
def load_from_kafka(*args, **kwargs):
    """Stream data from Kafka topic."""
    from mage_ai.streaming.sources.kafka import KafkaSource
    
    config = {
        "bootstrap_server": "localhost:9092",
        "topic": "user_events",
        "consumer_group": "mage_consumer",
    }
    source = KafkaSource(config)
    
    for messages in source.batch_read():
        yield messages
```

### Partitioning

```python
@data_loader
def load_partitioned_data(*args, **kwargs) -> pd.DataFrame:
    """Load data with date partitioning."""
    from datetime import datetime, timedelta
    
    execution_date = kwargs.get("execution_date", datetime.now())
    partition_date = execution_date.strftime("%Y-%m-%d")
    
    query = f"""
        SELECT * FROM events
        WHERE event_date = '{partition_date}'
    """
    return pd.read_sql(query, connection)
```

### Bài tập

1. **dbt integration** — Kết nối Mage với dbt project, chạy models qua Mage
2. **Streaming pipeline** — Tạo streaming pipeline đọc từ Kafka/API websocket
3. **Conditional logic** — Pipeline với branching (if sales > threshold → alert)
4. **Multi-output** — Block ghi ra cả database + file đồng thời

### Resources

- 📖 [Mage dbt Integration](https://docs.mage.ai/dbt/overview)
- 📖 [Streaming Pipelines](https://docs.mage.ai/guides/streaming/overview)
- 📖 [Templates Guide](https://docs.mage.ai/guides/blocks/custom-blocks)
- 💻 [Mage Examples](https://github.com/mage-ai/mage-ai/tree/master/mage_integrations)

---

## Level 3: Advanced (4–8 tuần)

### Custom Blocks

```python
# custom_blocks/my_custom_block.py
from mage_ai.data_preparation.decorators import custom

@custom
def execute_custom_logic(data: pd.DataFrame, *args, **kwargs) -> dict:
    """Custom block with multiple outputs."""
    valid = data[data["is_valid"] == True]
    invalid = data[data["is_valid"] == False]
    
    # Send alerts for invalid records
    if len(invalid) > 0:
        send_alert(f"Found {len(invalid)} invalid records")
    
    return {
        "valid_records": valid,
        "invalid_records": invalid,
        "stats": {
            "total": len(data),
            "valid": len(valid),
            "invalid": len(invalid)
        }
    }
```

### Kubernetes Deployment

```yaml
# docker-compose.yml (production)
version: "3"
services:
  mage:
    image: mageai/mageai:latest
    command: mage start my_project
    environment:
      - ENV=production
      - MAGE_DATABASE_CONNECTION_URL=postgresql://user:pass@db:5432/mage
      - AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
      - AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
    volumes:
      - ./my_project:/home/src/my_project
    ports:
      - "6789:6789"
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 4G
```

```yaml
# Kubernetes Helm values
mage:
  replicaCount: 2
  image:
    repository: mageai/mageai
    tag: latest
  resources:
    requests:
      memory: "2Gi"
      cpu: "1000m"
    limits:
      memory: "4Gi"
      cpu: "2000m"
  env:
    - name: ENV
      value: production
    - name: MAGE_DATABASE_CONNECTION_URL
      valueFrom:
        secretKeyRef:
          name: mage-secrets
          key: database-url
```

### CI/CD Pipeline

```yaml
# .github/workflows/mage-deploy.yml
name: Deploy Mage
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run tests
        run: |
          pip install mage-ai
          python -m pytest tests/
      
      - name: Build & Push Docker
        run: |
          docker build -t my-registry/mage:${{ github.sha }} .
          docker push my-registry/mage:${{ github.sha }}
      
      - name: Deploy to Kubernetes
        run: |
          helm upgrade --install mage ./helm/mage \
            --set image.tag=${{ github.sha }}
```

### Observability & Monitoring

```python
# Custom callback for monitoring
from mage_ai.orchestration.notification.config import NotificationConfig

notification_config = NotificationConfig(
    slack_config={
        "webhook_url": "https://hooks.slack.com/services/...",
        "channel": "#data-alerts",
    },
    alert_on=["failure", "sla_miss"],
)
```

### Bài tập

1. **Docker deploy** — Deploy Mage production với Docker Compose + external DB
2. **Custom block** — Viết custom block xử lý logic phức tạp (validation + routing)
3. **CI/CD** — Setup GitHub Actions test + deploy cho Mage project
4. **Multi-environment** — Configure dev/staging/prod environments

### Resources

- 📖 [Mage Deployment Guide](https://docs.mage.ai/production/deploying-to-cloud/architecture)
- 📖 [Kubernetes Deploy](https://docs.mage.ai/production/deploying-to-cloud/using-helm)
- 📖 [CI/CD Guide](https://docs.mage.ai/production/ci-cd/overview)
- 💻 [Mage Terraform Templates](https://github.com/mage-ai/mage-ai-terraform-templates)

---

## Alternatives & Công cụ tương đương

| Tool | Approach | Strengths | Weaknesses |
|------|----------|-----------|------------|
| **Mage** | Notebook-like blocks | Visual, fast dev, streaming | Newer, smaller community |
| **Airflow** | Task DAGs | Mature, scalable, many plugins | Complex setup, boilerplate |
| **Dagster** | Asset-oriented | Testing, lineage, type-safe | Steeper learning curve |
| **Prefect** | Python flows | Pythonic, dynamic, cloud-native | Less visual |
| **Kestra** | YAML declarative | Language-agnostic, event-driven | Less interactive |

### Khi nào chọn gì?

- **Mage** → Muốn phát triển nhanh, visual, team quen notebook workflow
- **Airflow** → Production lớn, cần mature ecosystem, đã có kinh nghiệm
- **Dagster** → Cần strong typing, testing framework, asset lineage
- **Prefect** → Pure Python team, dynamic workflows, minimal infrastructure
- **Kestra** → Declarative YAML, multi-language, event-driven patterns
