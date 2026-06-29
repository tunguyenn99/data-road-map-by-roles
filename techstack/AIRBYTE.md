# 🔄 Airbyte — Learning Guide (Beginner → Advanced)

## Tổng quan

**Airbyte** là open-source EL(T) platform cho data integration. Với 300+ pre-built connectors, Airbyte giúp replicate data từ sources (APIs, DBs, SaaS) sang destinations (warehouses, lakes) mà không cần viết custom code.

**Use cases:**
- Data replication từ production DBs → data warehouse
- SaaS data ingestion (Stripe, Salesforce, HubSpot → warehouse)
- CDC (Change Data Capture) cho real-time sync
- Custom API → warehouse pipelines

**Ai dùng:** Data Engineers, Analytics Engineers, Platform Engineers

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Source** | Nơi data đến từ (DB, API, SaaS app) |
| **Destination** | Nơi data được ghi vào (warehouse, lake) |
| **Connection** | Link giữa source + destination + config |
| **Sync Mode** | Cách sync: full refresh, incremental, CDC |
| **Catalog** | Schema/streams available từ source |
| **Stream** | Một table/endpoint trong source |
| **Normalization** | Auto-flatten nested JSON thành tables |

### Docker Install

```bash
# Clone Airbyte
git clone --depth=1 https://github.com/airbytehq/airbyte.git
cd airbyte

# Start (Docker required)
./run-ab-platform.sh

# Access UI at http://localhost:8000
# Default: airbyte / password
```

```bash
# Alternative: abctl (Airbyte CLI)
curl -LsfS https://get.airbyte.com | bash -
abctl local install

# Access at http://localhost:8000
```

### First Connection (PostgreSQL → Warehouse)

```
Step 1: Add Source
  - Type: PostgreSQL
  - Host: your-db-host
  - Port: 5432
  - Database: myapp
  - User: airbyte_reader (read-only user recommended)
  - Password: ***
  - SSL: require

Step 2: Add Destination
  - Type: BigQuery / Redshift / Snowflake / DuckDB (local)
  - Project: my-project
  - Dataset: raw_data
  - Credentials: service account JSON

Step 3: Create Connection
  - Select streams (tables) to sync
  - Choose sync mode per stream:
    - Full Refresh | Overwrite: mỗi lần sync replace hết
    - Full Refresh | Append: append mới mỗi lần
    - Incremental | Append: chỉ sync new/updated rows
    - Incremental | Deduped: incremental + dedup by primary key
  - Set sync frequency: every 1h, 6h, 24h, manual
  - Click "Set up connection"
```

### Sync Modes Explained

```
Full Refresh | Overwrite:
  Source: [A, B, C, D]
  Destination after sync: [A, B, C, D]  (replaced entirely)

Full Refresh | Append:
  Source: [A, B, C, D]
  Run 1: Destination = [A, B, C, D]
  Run 2: Destination = [A, B, C, D, A, B, C, D]  (duplicated!)

Incremental | Append:
  Cursor: updated_at
  Run 1: sync rows where updated_at > last_sync → Dest: [A, B]
  Run 2: sync rows where updated_at > last_sync → Dest: [A, B, C]

Incremental | Deduped:
  Same as above + dedup by primary key
  Final destination only has latest version of each record
```

### Bài tập

1. **Install** — Chạy Airbyte locally (Docker), access UI
2. **First sync** — Sync PostgreSQL table → local DuckDB/JSON
3. **Multiple streams** — Sync 5 tables, mỗi table khác sync mode
4. **Schedule** — Configure automatic sync every 6 hours

### Resources

- 📖 [Airbyte Docs](https://docs.airbyte.com/)
- 📖 [Quickstart Guide](https://docs.airbyte.com/using-airbyte/getting-started/)
- 💻 [Airbyte GitHub](https://github.com/airbytehq/airbyte)
- 📖 [Connector Catalog](https://docs.airbyte.com/integrations/)


---

## Level 2: Intermediate (2–4 tuần)

### CDC (Change Data Capture)

```
CDC dùng database's WAL (Write-Ahead Log) để capture real-time changes:

PostgreSQL CDC setup:
1. Source config:
   - Replication method: CDC (Debezium)
   - Replication slot: airbyte_slot
   - Publication: airbyte_publication
   
2. PostgreSQL requirements:
   - wal_level = logical
   - max_replication_slots >= 1
   - max_wal_senders >= 1
```

```sql
-- PostgreSQL: enable logical replication
ALTER SYSTEM SET wal_level = 'logical';
ALTER SYSTEM SET max_replication_slots = 5;
-- Restart PostgreSQL

-- Create replication user
CREATE USER airbyte_cdc WITH REPLICATION LOGIN PASSWORD 'secure_pass';
GRANT SELECT ON ALL TABLES IN SCHEMA public TO airbyte_cdc;

-- Create publication
CREATE PUBLICATION airbyte_publication FOR ALL TABLES;
```

### Custom Connectors (Low-code CDK)

```yaml
# connector_manifest.yaml — Low-code connector definition
version: "0.79.0"

type: DeclarativeSource
check:
  type: CheckStream
  stream_names: ["users"]

definitions:
  selector:
    type: RecordSelector
    extractor:
      type: DpathExtractor
      field_path: ["data"]

  requester:
    type: HttpRequester
    url_base: "https://api.example.com/v1"
    http_method: "GET"
    authenticator:
      type: BearerAuthenticator
      api_token: "{{ config['api_key'] }}"

  paginator:
    type: DefaultPaginator
    page_token_option:
      type: RequestOption
      inject_into: request_parameter
      field_name: "page"
    pagination_strategy:
      type: PageIncrement
      start_from_page: 1

streams:
  - type: DeclarativeStream
    name: "users"
    primary_key: "id"
    retriever:
      type: SimpleRetriever
      record_selector: "#/definitions/selector"
      requester: "#/definitions/requester"
      paginator: "#/definitions/paginator"
    schema_loader:
      type: InlineSchemaLoader
      schema:
        type: object
        properties:
          id: { type: integer }
          name: { type: string }
          email: { type: string }
          created_at: { type: string, format: date-time }

spec:
  type: Spec
  connection_specification:
    type: object
    required: ["api_key"]
    properties:
      api_key:
        type: string
        title: "API Key"
        airbyte_secret: true
```

### Transformations & dbt Integration

```yaml
# In Airbyte connection settings:
# Transformation tab > Add dbt transformation

# Airbyte raw data lands in: _airbyte_raw_<stream_name>
# Normalization creates: <stream_name> (flattened)

# Then dbt transforms normalized data:
# models/staging/stg_customers.sql
```

```sql
-- dbt model on Airbyte-synced data
SELECT
    _airbyte_unique_key AS customer_id,
    name,
    email,
    CAST(created_at AS TIMESTAMP) AS created_at,
    _airbyte_normalized_at AS synced_at
FROM {{ source('airbyte', 'customers') }}
WHERE _airbyte_normalized_at > (
    SELECT COALESCE(MAX(_airbyte_normalized_at), '1900-01-01')
    FROM {{ this }}
)
```

### Bài tập

1. **CDC setup** — Configure CDC for PostgreSQL, observe real-time changes
2. **Custom connector** — Build low-code connector for a public REST API
3. **dbt integration** — Airbyte sync → dbt transform → final tables
4. **Multiple sources** — Sync 3 different sources (DB + API + SaaS) to same warehouse

### Resources

- 📖 [CDC Guide](https://docs.airbyte.com/understanding-airbyte/cdc)
- 📖 [Connector Builder](https://docs.airbyte.com/connector-development/connector-builder-ui/overview)
- 📖 [Low-code CDK](https://docs.airbyte.com/connector-development/config-based/low-code-cdk-overview)
- 📖 [dbt Integration](https://docs.airbyte.com/operator-guides/transformation-and-normalization/transformations-with-dbt)

---

## Level 3: Advanced (4–8 tuần)

### Airbyte API (Programmatic Management)

```python
import requests

AIRBYTE_URL = "http://localhost:8000/api/v1"
headers = {"Content-Type": "application/json"}

# List workspaces
workspaces = requests.post(
    f"{AIRBYTE_URL}/workspaces/list",
    headers=headers
).json()

workspace_id = workspaces["workspaces"][0]["workspaceId"]

# Create source programmatically
source = requests.post(
    f"{AIRBYTE_URL}/sources/create",
    headers=headers,
    json={
        "workspaceId": workspace_id,
        "sourceDefinitionId": "decd338e-5647-4c0b-adf4-da0e75f5a750",  # PostgreSQL
        "connectionConfiguration": {
            "host": "db-host",
            "port": 5432,
            "database": "myapp",
            "username": "airbyte",
            "password": "secret",
            "ssl_mode": {"mode": "require"}
        },
        "name": "Production DB"
    }
).json()

# Trigger manual sync
sync = requests.post(
    f"{AIRBYTE_URL}/connections/sync",
    headers=headers,
    json={"connectionId": "connection-uuid"}
).json()

print(f"Sync job: {sync['job']['id']}")
```

### Kubernetes Deployment

```yaml
# values.yaml for Airbyte Helm chart
global:
  serviceAccountName: airbyte
  database:
    host: rds-endpoint
    port: 5432
    database: airbyte
    user: airbyte
    password: secret
  logs:
    storage:
      type: S3
      s3:
        bucket: airbyte-logs
        region: us-east-1

webapp:
  replicaCount: 1
  resources:
    requests:
      cpu: "250m"
      memory: "512Mi"

server:
  replicaCount: 1
  resources:
    requests:
      cpu: "500m"
      memory: "1Gi"

worker:
  replicaCount: 2
  resources:
    requests:
      cpu: "1000m"
      memory: "2Gi"
    limits:
      cpu: "2000m"
      memory: "4Gi"
```

```bash
# Deploy
helm repo add airbyte https://airbytehq.github.io/helm-charts
helm install airbyte airbyte/airbyte -f values.yaml -n airbyte --create-namespace
```

### Connector Development (Python)

```python
# Custom source connector (full Python)
from airbyte_cdk.sources import AbstractSource
from airbyte_cdk.sources.streams import Stream
from airbyte_cdk.sources.streams.http import HttpStream
from airbyte_cdk.models import SyncMode
import requests

class MyApiStream(HttpStream):
    url_base = "https://api.example.com/v2/"
    primary_key = "id"
    
    def __init__(self, api_key: str, **kwargs):
        super().__init__(**kwargs)
        self.api_key = api_key
    
    def path(self, **kwargs) -> str:
        return "users"
    
    def request_headers(self, **kwargs) -> dict:
        return {"Authorization": f"Bearer {self.api_key}"}
    
    def parse_response(self, response, **kwargs):
        data = response.json()
        yield from data.get("results", [])
    
    def next_page_token(self, response) -> dict | None:
        data = response.json()
        if data.get("has_more"):
            return {"cursor": data["next_cursor"]}
        return None
    
    def request_params(self, next_page_token=None, **kwargs) -> dict:
        params = {"limit": 100}
        if next_page_token:
            params["cursor"] = next_page_token["cursor"]
        return params

class SourceMyApi(AbstractSource):
    def check_connection(self, logger, config) -> tuple[bool, any]:
        try:
            resp = requests.get(
                "https://api.example.com/v2/health",
                headers={"Authorization": f"Bearer {config['api_key']}"}
            )
            return resp.status_code == 200, None
        except Exception as e:
            return False, str(e)
    
    def streams(self, config) -> list[Stream]:
        return [MyApiStream(api_key=config["api_key"])]
```

### Bài tập

1. **API automation** — Script tạo sources/destinations/connections via API
2. **K8s deploy** — Deploy Airbyte production trên Kubernetes
3. **Custom connector** — Build full Python connector cho internal API
4. **Monitoring** — Setup alerts cho failed syncs (Slack/PagerDuty)

### Resources

- 📖 [Airbyte API Reference](https://docs.airbyte.com/api-documentation)
- 📖 [Kubernetes Deploy](https://docs.airbyte.com/deploying-airbyte/on-kubernetes)
- 📖 [Python CDK](https://docs.airbyte.com/connector-development/cdk-python/)
- 📖 [Airbyte Cloud](https://docs.airbyte.com/cloud/getting-started-with-airbyte-cloud)
- 💻 [Connector Examples](https://github.com/airbytehq/airbyte/tree/master/airbyte-integrations/connectors)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **Airbyte** | Open-source ELT | 300+ connectors, self-host, CDC | Resource-heavy, complex deploy |
| **Fivetran** | Managed SaaS | Zero maintenance, reliable, fast setup | Expensive, vendor lock-in |
| **dlt** | Python library | Lightweight, pip install, flexible | DIY connectors, no UI |
| **Stitch** | Managed SaaS | Simple, affordable | Limited connectors, basic |
| **Hevo** | Managed SaaS | No-code, good UI | Pricing, fewer connectors |
| **Meltano** | Open-source CLI | Singer ecosystem, git-based | Smaller community, CLI-only |

### Khi nào chọn gì?

- **Airbyte** → Self-hosted preference, cần nhiều connectors, budget-conscious, CDC
- **Fivetran** → Enterprise cần zero-maintenance, reliability over cost
- **dlt** → Python developers, lightweight pipelines, custom sources
- **Stitch** → Simple use case, moderate budget, quick setup
- **Meltano** → GitOps workflow, Singer taps ecosystem, CLI preference
