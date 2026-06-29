# 🔶 Dagster — Learning Guide (Beginner → Advanced)

## Tổng quan

**Dagster** là một modern data orchestrator theo hướng **asset-oriented** — thay vì nghĩ về "tasks chạy theo thứ tự" (như Airflow), bạn nghĩ về "data assets cần được tạo ra". Dagster giúp bạn định nghĩa, test, và monitor toàn bộ data pipeline một cách declarative.

**Use cases:**
- Orchestrate ETL/ELT pipelines
- dbt integration (dagster-dbt)
- ML pipelines (feature engineering → training → serving)
- Data quality monitoring

**Ai dùng:** Data Engineers, Analytics Engineers, ML Engineers

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Asset** | Một data object (table, file, ML model) mà pipeline tạo ra |
| **Op** | Một unit of computation (function) |
| **Job** | Một collection of ops/assets để execute |
| **Schedule** | Cron-based trigger cho jobs |
| **Sensor** | Event-based trigger (file appears, API returns data) |
| **Resource** | External service connections (DB, API, cloud storage) |
| **IO Manager** | Cách đọc/ghi assets (to/from storage) |

### Setup & First Asset

```bash
# Install
pip install dagster dagster-webserver

# Scaffold project
dagster project scaffold --name my_project
cd my_project
pip install -e ".[dev]"

# Start local dev server
dagster dev
```

```python
# my_project/assets.py
from dagster import asset
import pandas as pd

@asset
def raw_customers() -> pd.DataFrame:
    """Load raw customer data from CSV."""
    return pd.read_csv("data/customers.csv")

@asset
def cleaned_customers(raw_customers: pd.DataFrame) -> pd.DataFrame:
    """Clean and transform customer data."""
    df = raw_customers.copy()
    df["email"] = df["email"].str.lower().str.strip()
    df = df.dropna(subset=["email"])
    return df

@asset
def customer_summary(cleaned_customers: pd.DataFrame) -> pd.DataFrame:
    """Aggregate customer metrics."""
    return cleaned_customers.groupby("country").agg(
        total_customers=("id", "count"),
        avg_age=("age", "mean")
    ).reset_index()
```

```python
# my_project/__init__.py
from dagster import Definitions, load_assets_from_modules
from . import assets

all_assets = load_assets_from_modules([assets])

defs = Definitions(assets=all_assets)
```

### First Job with Ops

```python
from dagster import op, job, Out, In

@op
def extract_data() -> dict:
    """Extract data from source."""
    return {"users": [{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}]}

@op
def transform_data(raw_data: dict) -> list:
    """Transform extracted data."""
    return [
        {**user, "name_upper": user["name"].upper()}
        for user in raw_data["users"]
    ]

@op
def load_data(transformed: list) -> None:
    """Load data to destination."""
    for record in transformed:
        print(f"Loading: {record}")

@job
def etl_job():
    load_data(transform_data(extract_data()))
```

### Bài tập

1. **Scaffold project** — Tạo project mới, chạy `dagster dev`, explore UI tại `localhost:3000`
2. **3 assets pipeline** — Tạo 3 assets: raw → cleaned → aggregated (dùng pandas)
3. **Schedule** — Thêm schedule chạy daily cho pipeline
4. **Materialize** — Dùng UI để materialize từng asset, xem lineage graph

### Resources

- 📖 [Dagster Docs — Getting Started](https://docs.dagster.io/getting-started)
- 📖 [Dagster Tutorial](https://docs.dagster.io/tutorial)
- 🎬 [Dagster YouTube Channel](https://www.youtube.com/@dagaboratory)
- 💻 [Dagster GitHub](https://github.com/dagster-io/dagster)

---

## Level 2: Intermediate (2–4 tuần)

### Partitions

```python
from dagster import asset, DailyPartitionsDefinition, AssetExecutionContext

daily_partitions = DailyPartitionsDefinition(start_date="2024-01-01")

@asset(partitions_def=daily_partitions)
def daily_orders(context: AssetExecutionContext) -> pd.DataFrame:
    """Load orders for a specific date partition."""
    partition_date = context.partition_key
    context.log.info(f"Processing orders for {partition_date}")
    
    query = f"""
        SELECT * FROM orders 
        WHERE order_date = '{partition_date}'
    """
    # Simulated — replace with actual DB query
    return pd.DataFrame({"date": [partition_date], "total": [100]})
```

### Asset Groups & Key Prefixes

```python
from dagster import asset, AssetKey

@asset(group_name="raw", key_prefix=["bronze"])
def raw_transactions() -> pd.DataFrame:
    return pd.read_parquet("s3://bucket/raw/transactions.parquet")

@asset(group_name="transformed", key_prefix=["silver"])
def clean_transactions(raw_transactions: pd.DataFrame) -> pd.DataFrame:
    return raw_transactions.dropna()

@asset(group_name="analytics", key_prefix=["gold"])
def transaction_summary(clean_transactions: pd.DataFrame) -> pd.DataFrame:
    return clean_transactions.groupby("category").sum()
```

### Sensors

```python
from dagster import sensor, RunRequest, SensorEvaluationContext
import os

@sensor(job=etl_job, minimum_interval_seconds=30)
def file_sensor(context: SensorEvaluationContext):
    """Trigger job when new file appears."""
    watch_dir = "/data/incoming"
    for filename in os.listdir(watch_dir):
        filepath = os.path.join(watch_dir, filename)
        if filename.endswith(".csv"):
            yield RunRequest(
                run_key=filename,
                run_config={
                    "ops": {
                        "extract_data": {
                            "config": {"filepath": filepath}
                        }
                    }
                }
            )
```

### dagster-dbt Integration

```python
from dagster import asset, AssetExecutionContext
from dagster_dbt import DbtCliResource, dbt_assets, DbtProject

my_dbt_project = DbtProject(
    project_dir="path/to/dbt/project",
    packaged_project_dir="path/to/dbt/project",
)

@dbt_assets(manifest=my_dbt_project.manifest_path)
def my_dbt_assets(context: AssetExecutionContext, dbt: DbtCliResource):
    yield from dbt.cli(["build"], context=context).stream()
```

```python
# definitions.py
from dagster import Definitions
from dagster_dbt import DbtCliResource

defs = Definitions(
    assets=[my_dbt_assets],
    resources={
        "dbt": DbtCliResource(project_dir=my_dbt_project),
    },
)
```

### Bài tập

1. **Partitioned asset** — Tạo daily-partitioned asset, backfill 7 ngày qua UI
2. **Sensor** — Viết sensor trigger khi có file mới trong folder
3. **Asset groups** — Tổ chức assets theo bronze/silver/gold layers
4. **dbt integration** — Kết nối dagster-dbt với project dbt có sẵn

### Resources

- 📖 [Dagster Partitions Guide](https://docs.dagster.io/concepts/partitions-schedules-sensors/partitions)
- 📖 [dagster-dbt Docs](https://docs.dagster.io/integrations/dbt)
- 📖 [Sensors Guide](https://docs.dagster.io/concepts/partitions-schedules-sensors/sensors)
- 🎬 [Dagster + dbt Tutorial](https://docs.dagster.io/integrations/dbt/using-dbt-with-dagster)

---

## Level 3: Advanced (4–8 tuần)

### Multi-Code Locations

```yaml
# workspace.yaml
load_from:
  - python_module:
      module_name: team_a_project
      location_name: team_a
  - python_module:
      module_name: team_b_project
      location_name: team_b
  - grpc_server:
      host: remote-host
      port: 4266
      location_name: team_c_remote
```

### Custom IO Manager

```python
from dagster import IOManager, io_manager, InputContext, OutputContext
import boto3
import pyarrow.parquet as pq
import pyarrow as pa

class S3ParquetIOManager(IOManager):
    def __init__(self, bucket: str, prefix: str):
        self.bucket = bucket
        self.prefix = prefix
        self.s3 = boto3.client("s3")
    
    def _get_path(self, context) -> str:
        asset_key = "/".join(context.asset_key.path)
        return f"{self.prefix}/{asset_key}.parquet"
    
    def handle_output(self, context: OutputContext, obj: pd.DataFrame):
        path = self._get_path(context)
        table = pa.Table.from_pandas(obj)
        buffer = pa.BufferOutputStream()
        pq.write_table(table, buffer)
        self.s3.put_object(
            Bucket=self.bucket,
            Key=path,
            Body=buffer.getvalue().to_pybytes()
        )
        context.log.info(f"Wrote {len(obj)} rows to s3://{self.bucket}/{path}")
    
    def load_input(self, context: InputContext) -> pd.DataFrame:
        path = self._get_path(context)
        response = self.s3.get_object(Bucket=self.bucket, Key=path)
        table = pq.read_table(response["Body"])
        return table.to_pandas()

@io_manager(config_schema={"bucket": str, "prefix": str})
def s3_parquet_io_manager(context):
    return S3ParquetIOManager(
        bucket=context.resource_config["bucket"],
        prefix=context.resource_config["prefix"],
    )
```

### Branch Deployments (Dagster Cloud)

```yaml
# dagster_cloud.yaml
locations:
  - location_name: my_project
    code_source:
      python_file: my_project/definitions.py
    build:
      directory: ./
      registry: ghcr.io/my-org/my-project

# Branch deployments auto-create preview environments per PR
```

### Dagster Cloud Hybrid Architecture

```python
# Hybrid deployment — agent runs in your infra
# dagster_cloud.yaml
agent:
  module: dagster_cloud_agent
  config:
    agent_type: ecs  # or kubernetes, docker
    
locations:
  - location_name: analytics
    code_source:
      python_file: analytics/definitions.py
    container_context:
      ecs:
        run_resources:
          cpu: "2048"
          memory: "4096"
```

### Bài tập

1. **Custom IO Manager** — Viết IO Manager ghi assets ra S3/GCS dạng Parquet
2. **Multi-code location** — Setup workspace với 2+ code locations riêng biệt
3. **Branch deployment** — Configure branch deployment cho CI/CD (GitHub Actions)
4. **Monitoring** — Setup alerts khi asset materialization fail

### Resources

- 📖 [Dagster Cloud Docs](https://docs.dagster.io/dagster-cloud)
- 📖 [IO Managers Guide](https://docs.dagster.io/concepts/io-management/io-managers)
- 📖 [Multi-Code Locations](https://docs.dagster.io/concepts/code-locations)
- 💻 [dagster-cloud GitHub](https://github.com/dagster-io/dagster-cloud)
- 📖 [Branch Deployments](https://docs.dagster.io/dagster-cloud/managing-deployments/branch-deployments)

---

## Alternatives & Công cụ tương đương

| Tool | Approach | Strengths | Weaknesses |
|------|----------|-----------|------------|
| **Dagster** | Asset-oriented | Type safety, testing, lineage, dbt-native | Learning curve, newer ecosystem |
| **Airflow** | Task-oriented (DAGs) | Mature, huge community, many providers | Boilerplate, harder testing |
| **Prefect** | Flow-oriented | Pythonic, easy start, hybrid cloud | Smaller community vs Airflow |
| **Mage** | Notebook-like blocks | Visual, fast prototyping | Less production-hardened |
| **Kestra** | YAML declarative | Language-agnostic, event-driven | Less Python-native |

### Khi nào chọn gì?

- **Dagster** → Bạn cần asset lineage, dbt integration mạnh, muốn test pipeline dễ dàng
- **Airflow** → Team đã quen, cần mature ecosystem, many integrations
- **Prefect** → Muốn đơn giản, Pythonic, dynamic workflows
- **Mage** → Prototyping nhanh, team thích notebook-style
- **Kestra** → Multi-language team, event-driven architecture, YAML preference
