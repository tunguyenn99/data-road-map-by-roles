# 🐍 dlt (data load tool) — Learning Guide (Beginner → Advanced)

## Tổng quan

**dlt** là Python-first ELT library — chỉ cần `pip install dlt` là có thể build data pipelines. Không cần infrastructure riêng, không cần UI phức tạp. dlt tự động handle schema evolution, typing, và incremental loading.

**Use cases:**
- API data ingestion (REST APIs → warehouse/lake)
- Database replication (lightweight alternative to Airbyte)
- Custom ETL scripts có cấu trúc
- Serverless data pipelines (deploy trên GitHub Actions, Airflow)

**Ai dùng:** Data Engineers, Python Developers, solo devs muốn ELT đơn giản

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Source** | Python function yield data (generator) |
| **Resource** | Một stream of data trong source |
| **Pipeline** | Orchestrates source → destination |
| **Destination** | Nơi data được load (DuckDB, BigQuery, Postgres...) |
| **Schema** | Auto-inferred from data, evolves automatically |
| **State** | Tracks incremental loading progress |

### Setup & First Pipeline

```bash
# Install
pip install dlt[duckdb]  # duckdb as local destination

# Init a pipeline
dlt init chess duckdb
# Creates: chess_pipeline.py, .dlt/config.toml, .dlt/secrets.toml
```

```python
# my_first_pipeline.py
import dlt
import requests

@dlt.source
def github_source(username: str):
    """Load GitHub user data."""
    
    @dlt.resource(write_disposition="replace")
    def repos():
        """Get user's public repositories."""
        url = f"https://api.github.com/users/{username}/repos"
        response = requests.get(url, params={"per_page": 100})
        yield from response.json()
    
    @dlt.resource(write_disposition="replace")
    def profile():
        """Get user profile."""
        url = f"https://api.github.com/users/{username}"
        response = requests.get(url)
        yield response.json()
    
    return repos, profile

# Create and run pipeline
pipeline = dlt.pipeline(
    pipeline_name="github_pipeline",
    destination="duckdb",
    dataset_name="github_data"
)

# Load data
load_info = pipeline.run(github_source(username="torvalds"))
print(load_info)
```

### Explore Loaded Data

```python
# After running pipeline, explore with DuckDB
import duckdb

# dlt creates a .duckdb file
conn = duckdb.connect("github_pipeline.duckdb")

# See tables created
conn.sql("SHOW TABLES").show()

# Query data
conn.sql("""
    SELECT name, stargazers_count, language
    FROM github_data.repos
    ORDER BY stargazers_count DESC
    LIMIT 10
""").show()

# Or use dlt's built-in exploration
pipeline = dlt.pipeline(pipeline_name="github_pipeline", destination="duckdb")
with pipeline.sql_client() as client:
    result = client.execute_sql("SELECT * FROM repos LIMIT 5")
    for row in result:
        print(row)
```

### Bài tập

1. **First pipeline** — pip install dlt, load GitHub repos → DuckDB
2. **REST API** — Build pipeline cho public API (JSONPlaceholder, OpenWeather)
3. **Multiple resources** — Source với 3+ resources, load to same dataset
4. **Explore** — Dùng DuckDB query loaded data, check schema dlt tạo

### Resources

- 📖 [dlt Docs](https://dlthub.com/docs)
- 📖 [Getting Started](https://dlthub.com/docs/getting-started)
- 💻 [dlt GitHub](https://github.com/dlt-hub/dlt)
- 📖 [Verified Sources](https://dlthub.com/docs/dlt-ecosystem/verified-sources)
- 🎬 [dlt Blog & Tutorials](https://dlthub.com/docs/blog)


---

## Level 2: Intermediate (2–4 tuần)

### Custom Sources with Pagination

```python
import dlt
import requests
from dlt.sources.helpers import requests as dlt_requests

@dlt.source
def ecommerce_api(api_key: str = dlt.secrets.value):
    """Custom e-commerce API source with pagination."""
    
    @dlt.resource(write_disposition="merge", primary_key="id")
    def products():
        """Load all products with pagination."""
        page = 1
        while True:
            response = requests.get(
                "https://api.store.com/v1/products",
                headers={"Authorization": f"Bearer {api_key}"},
                params={"page": page, "per_page": 100}
            )
            data = response.json()
            if not data["items"]:
                break
            yield from data["items"]
            page += 1
    
    @dlt.resource(write_disposition="append")
    def orders():
        """Load orders (append-only)."""
        response = requests.get(
            "https://api.store.com/v1/orders",
            headers={"Authorization": f"Bearer {api_key}"},
            params={"status": "completed"}
        )
        yield from response.json()["orders"]
    
    return products, orders
```

### Incremental Loading

```python
import dlt
from dlt.sources.incremental import Incremental

@dlt.source
def incremental_source():
    
    @dlt.resource(
        write_disposition="merge",
        primary_key="id"
    )
    def users(
        updated_at: dlt.sources.incremental[str] = dlt.sources.incremental(
            "updated_at",
            initial_value="2024-01-01T00:00:00Z"
        )
    ):
        """Only load users modified since last run."""
        response = requests.get(
            "https://api.example.com/users",
            params={
                "modified_after": updated_at.last_value,
                "order_by": "updated_at"
            }
        )
        yield from response.json()["users"]

# First run: loads all users since 2024-01-01
# Second run: only loads users modified since last sync
pipeline = dlt.pipeline(
    pipeline_name="incremental_demo",
    destination="duckdb",
    dataset_name="users"
)

load_info = pipeline.run(incremental_source())
print(f"Loaded: {load_info}")
```

### Schema Contracts

```python
import dlt

# Define schema contract (strict mode)
@dlt.resource(
    write_disposition="merge",
    primary_key="id",
    columns={
        "id": {"data_type": "bigint", "nullable": False},
        "name": {"data_type": "text", "nullable": False},
        "email": {"data_type": "text", "nullable": True},
        "amount": {"data_type": "decimal", "nullable": False},
    }
)
def strict_orders():
    yield from get_orders()

# Schema evolution strategies
pipeline = dlt.pipeline(
    pipeline_name="contract_demo",
    destination="bigquery",
    dataset_name="production"
)

# Freeze schema — new columns rejected
pipeline.run(
    strict_orders(),
    schema_contract={"columns": "freeze", "data_type": "freeze"}
)

# Evolve schema — new columns added automatically (default)
pipeline.run(
    strict_orders(),
    schema_contract={"columns": "evolve", "data_type": "evolve"}
)

# Discard row — rows not matching schema are dropped
pipeline.run(
    strict_orders(),
    schema_contract={"columns": "discard_row"}
)
```

### Deploy to Airflow / GitHub Actions

```python
# Deploy to GitHub Actions
# dlt creates deployment files automatically
# dlt deploy my_pipeline.py github-action --schedule "0 6 * * *"
```

```yaml
# .github/workflows/dlt_pipeline.yml (auto-generated)
name: Run dlt pipeline
on:
  schedule:
    - cron: "0 6 * * *"  # Daily at 6 AM UTC
  workflow_dispatch: {}

jobs:
  run_pipeline:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run pipeline
        env:
          DESTINATION__BIGQUERY__CREDENTIALS: ${{ secrets.BQ_CREDENTIALS }}
          SOURCES__API_KEY: ${{ secrets.API_KEY }}
        run: python my_pipeline.py
```

```python
# Deploy to Airflow
# dlt deploy my_pipeline.py airflow-composer
```

### Bài tập

1. **Custom source** — Build source cho REST API với pagination + auth
2. **Incremental** — Pipeline incremental loading, run 2 lần, verify only new data
3. **Schema contracts** — Test freeze vs evolve khi source schema changes
4. **GitHub Actions** — Deploy pipeline chạy daily trên GitHub Actions

### Resources

- 📖 [Incremental Loading](https://dlthub.com/docs/general-usage/incremental-loading)
- 📖 [Schema Contracts](https://dlthub.com/docs/general-usage/schema-contracts)
- 📖 [Deployment](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- 📖 [REST API Source](https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api)

---

## Level 3: Advanced (4–8 tuần)

### Performance Tuning

```python
import dlt
from dlt.common import pendulum

@dlt.source
def high_volume_source():
    
    @dlt.resource(
        write_disposition="append",
        # Performance: parallel extraction
        parallelized=True,
    )
    def large_dataset():
        """Extract large dataset efficiently."""
        # Yield in batches for memory efficiency
        for page in range(100):
            batch = fetch_batch(page, size=10000)
            yield batch  # yield entire list, not individual items

# Pipeline with performance config
pipeline = dlt.pipeline(
    pipeline_name="high_perf",
    destination="bigquery",
    dataset_name="analytics",
)

# Configure for performance
load_info = pipeline.run(
    high_volume_source(),
    loader_file_format="parquet",  # Use Parquet for large volumes
    write_disposition="append",
)

# Check load statistics
print(f"Loaded {load_info.loads_ids}")
for package in load_info.load_packages:
    for table in package.tables.values():
        print(f"  {table['name']}: {table.get('rows_count', 'N/A')} rows")
```

### Custom Destinations

```python
import dlt
from dlt.destinations import filesystem

# Filesystem destination (S3, GCS, local)
pipeline = dlt.pipeline(
    pipeline_name="to_s3",
    destination=filesystem("s3://my-bucket/data/"),
    dataset_name="raw"
)

# Custom destination function
@dlt.destination(batch_size=1000)
def my_custom_destination(items, table):
    """Custom destination — write anywhere."""
    # items is a list of dicts
    # table contains schema info
    
    import elasticsearch
    es = elasticsearch.Elasticsearch(["http://localhost:9200"])
    
    actions = [
        {"_index": table["name"], "_source": item}
        for item in items
    ]
    
    from elasticsearch.helpers import bulk
    bulk(es, actions)
    
    return f"Loaded {len(items)} items to ES index {table['name']}"

# Use custom destination
pipeline = dlt.pipeline(
    pipeline_name="to_es",
    destination=my_custom_destination,
)
pipeline.run(my_source())
```

### State Management

```python
import dlt

@dlt.source
def stateful_source():
    
    @dlt.resource
    def events(
        last_event_id=dlt.sources.incremental("id", initial_value=0)
    ):
        """Load events with state tracking."""
        # dlt automatically manages state between runs
        # Stored in: .dlt/pipeline_state
        
        events = fetch_events(after_id=last_event_id.last_value)
        yield from events

# Inspect pipeline state
pipeline = dlt.pipeline(pipeline_name="stateful", destination="duckdb")
state = pipeline.state
print(f"Last run: {state.get('_state_version')}")
print(f"Sources state: {state.get('sources', {})}")

# Reset state (re-run from scratch)
pipeline.drop()  # Drops destination tables + state
```

### REST API Source Helper

```python
import dlt
from dlt.sources.rest_api import rest_api_source

# Declarative REST API configuration
source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["sources.api.token"],
        },
        "paginator": {
            "type": "json_link",
            "next_url_path": "pagination.next_url",
        },
    },
    "resources": [
        {
            "name": "customers",
            "endpoint": {
                "path": "customers",
                "params": {"per_page": 100},
            },
            "primary_key": "id",
            "write_disposition": "merge",
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "customers/{customer_id}/orders",
                "params": {"status": "completed"},
            },
            "include_from_parent": ["id"],
            "primary_key": "order_id",
            "write_disposition": "append",
        },
    ],
})

pipeline = dlt.pipeline(
    pipeline_name="rest_api",
    destination="bigquery",
    dataset_name="ecommerce"
)
load_info = pipeline.run(source)
```

### Bài tập

1. **Performance** — Pipeline loading 1M+ rows, optimize with batching + Parquet
2. **Custom destination** — Write dlt destination cho Elasticsearch hoặc Redis
3. **REST API helper** — Configure declarative source cho complex API (nested resources)
4. **State management** — Build pipeline with state, simulate failure + recovery

### Resources

- 📖 [Performance Guide](https://dlthub.com/docs/reference/performance)
- 📖 [Custom Destinations](https://dlthub.com/docs/dlt-ecosystem/destinations/destination)
- 📖 [REST API Source](https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api)
- 📖 [State & Incremental](https://dlthub.com/docs/general-usage/state)
- 💻 [dlt Examples](https://github.com/dlt-hub/dlt/tree/devel/docs/examples)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **dlt** | Python library | Lightweight, pip install, flexible, free | No UI, DIY connector maintenance |
| **Airbyte** | Open-source platform | 300+ connectors, UI, CDC | Heavy infra, complex deploy |
| **Fivetran** | Managed SaaS | Zero maintenance, reliable | Expensive, less customizable |
| **Meltano** | CLI + Singer | GitOps, Singer ecosystem | Smaller community, Singer quirks |
| **Singer taps** | Spec/protocol | Large tap catalog, standardized | Unmaintained taps, slow |

### Khi nào chọn gì?

- **dlt** → Python devs, lightweight needs, serverless deploy, custom sources, cost = $0
- **Airbyte** → Need UI, many connectors, CDC, team collaboration
- **Fivetran** → Enterprise, zero-maintenance, budget available
- **Meltano** → GitOps preference, Singer ecosystem, CLI-first
- **Singer** → Specific tap exists and works, already in ecosystem
