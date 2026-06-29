# 🔌 Fivetran — Learning Guide (Beginner → Advanced)

## Tổng quan

**Fivetran** là managed ELT platform (SaaS), chuyên replicate data từ sources → warehouse với zero maintenance. Connectors được Fivetran build và maintain — bạn chỉ cần configure và data flows tự động.

**Use cases:**
- SaaS data integration (Salesforce, HubSpot, Stripe → warehouse)
- Database replication (PostgreSQL, MySQL → Snowflake/BigQuery)
- Event data collection
- Enterprise data consolidation

**Ai dùng:** Data Engineers, Analytics Engineers, Data Teams muốn focus vào transformation thay vì ingestion

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Connector** | Pre-built integration (source → destination) |
| **Destination** | Target warehouse (Snowflake, BigQuery, Redshift) |
| **Sync frequency** | How often data syncs (5min → 24h) |
| **Schema** | Fivetran creates schemas in destination |
| **Transformations** | dbt models chạy after sync |
| **MAR** | Monthly Active Rows (pricing metric) |
| **Log** | Sync history & status |

### Free Trial Setup

```
1. Sign up: https://fivetran.com/signup (14-day free trial)
2. Add Destination:
   - Choose: BigQuery / Snowflake / Redshift / Databricks
   - Provide credentials
   - Fivetran tests connection
3. Add Connector:
   - Browse 300+ connectors
   - Easy ones to start: Google Sheets, PostgreSQL, GitHub
   - Authorize / provide credentials
   - Select schemas/tables to sync
4. First Sync:
   - Click "Start Initial Sync"
   - Monitor in dashboard
   - Data appears in destination within minutes
```

### Connect Source (Google Sheets → BigQuery)

```
Step 1: Connectors > + Add Connector
Step 2: Search "Google Sheets"
Step 3: Authorize Google account
Step 4: Select spreadsheet + worksheet
Step 5: Configure:
  - Destination schema: google_sheets
  - Sync frequency: 6 hours
  - Named range (optional)
Step 6: Save & Test → Start Sync

Result in BigQuery:
  project.google_sheets.worksheet_name
  Columns auto-detected from sheet headers
```

### Connect Source (PostgreSQL → Warehouse)

```
Step 1: Connectors > + Add Connector > PostgreSQL
Step 2: Configure:
  - Host: db.example.com
  - Port: 5432
  - Database: production
  - User: fivetran_reader (read-only!)
  - Password: ***
  - Connection method: Direct / SSH tunnel / VPN
Step 3: Select schemas & tables
Step 4: Choose sync mode:
  - Standard: periodic full/incremental sync
  - CDC (WAL): real-time log-based replication
Step 5: Set sync frequency: 15 min / 1h / 6h / 24h
Step 6: Save & Sync

Destination structure:
  warehouse.postgres_schema.table_name
  + _fivetran_synced (metadata column)
  + _fivetran_deleted (soft delete tracking)
```

### Bài tập

1. **Trial setup** — Đăng ký trial, connect destination (free tier warehouse)
2. **First connector** — Sync Google Sheets → warehouse, verify data
3. **Database connector** — Sync PostgreSQL (3 tables) → warehouse
4. **Monitor** — Explore sync logs, understand schema created by Fivetran

### Resources

- 📖 [Fivetran Docs](https://fivetran.com/docs)
- 📖 [Getting Started](https://fivetran.com/docs/getting-started)
- 📖 [Connector List](https://fivetran.com/docs/connectors)
- 🎬 [Fivetran YouTube](https://www.youtube.com/@fiaboretran)
- 📖 [Setup Guides](https://fivetran.com/docs/getting-started/core-concepts)


---

## Level 2: Intermediate (2–4 tuần)

### dbt Integration

```yaml
# Fivetran + dbt Cloud:
# 1. Settings > Transformations > Connect dbt Cloud
# 2. Provide dbt Cloud API key + account ID
# 3. Select dbt jobs to trigger after sync

# Fivetran triggers dbt run after each successful sync
# Flow: Source → Fivetran sync → dbt transform → clean tables
```

```sql
-- dbt model on Fivetran-synced data
-- models/staging/stg_stripe_payments.sql
WITH source AS (
    SELECT * FROM {{ source('stripe', 'payment_intent') }}
    WHERE NOT _fivetran_deleted  -- exclude soft-deleted records
)

SELECT
    id AS payment_id,
    amount / 100.0 AS amount_usd,  -- Stripe stores in cents
    currency,
    status,
    customer_id,
    created AS created_at,
    _fivetran_synced AS synced_at
FROM source
WHERE status = 'succeeded'
```

```yaml
# dbt source definition
# models/staging/_sources.yml
version: 2
sources:
  - name: stripe
    database: "{{ env_var('WAREHOUSE_DB') }}"
    schema: stripe  # Fivetran-managed schema
    freshness:
      warn_after: { count: 12, period: hour }
      error_after: { count: 24, period: hour }
    loaded_at_field: _fivetran_synced
    tables:
      - name: payment_intent
      - name: customer
      - name: subscription
```

### Transformation Scheduling

```
Fivetran Transformations:
1. Quick Start models (pre-built dbt packages):
   - fivetran/stripe: stripe__balance_transactions, stripe__customers
   - fivetran/hubspot: hubspot__contacts, hubspot__deals
   - fivetran/salesforce: salesforce__opportunity_enhanced

2. Install: Transformations > Quickstart > select package
3. Fivetran runs dbt after each sync automatically

Custom scheduling:
- After every sync (connector-triggered)
- On a schedule (e.g., hourly)
- After specific connectors finish
```

### Usage-Based Pricing & Optimization

```
Pricing model (MAR = Monthly Active Rows):
- Free tier: 500K MAR/month
- Standard: per-MAR pricing
- Enterprise: custom

Optimization strategies:
1. Sync only needed tables (don't sync ALL schemas)
2. Use incremental sync (not full refresh)
3. Reduce sync frequency for non-critical data
4. Block columns with PII you don't need
5. Use column hashing for PII columns
6. Monitor MAR usage in dashboard

MAR counting:
- Each new/updated row in a sync = 1 MAR
- Unchanged rows don't count
- Full refresh syncs count ALL rows every time!
```

### Custom Connectors (Fivetran Functions)

```python
# Fivetran Function connector (AWS Lambda / GCP Cloud Function)
# Allows custom data sources not in Fivetran's catalog

import json
from datetime import datetime

def lambda_handler(event, context):
    """Fivetran Function connector for custom API."""
    
    # Parse Fivetran request
    state = event.get("state", {})
    secrets = event.get("secrets", {})
    
    # Get data from your custom API
    api_key = secrets["api_key"]
    last_cursor = state.get("cursor", "")
    
    # Fetch data (example)
    import requests
    response = requests.get(
        "https://internal-api.company.com/data",
        headers={"Authorization": f"Bearer {api_key}"},
        params={"after": last_cursor, "limit": 1000}
    )
    data = response.json()
    
    # Return in Fivetran format
    return {
        "state": {"cursor": data["next_cursor"]},
        "insert": {
            "custom_events": data["events"]  # table_name: [records]
        },
        "schema": {
            "custom_events": {
                "primary_key": ["event_id"]
            }
        },
        "hasMore": data["has_more"]
    }
```

### Bài tập

1. **dbt integration** — Connect dbt Cloud, trigger transformation after sync
2. **Quickstart models** — Install Fivetran dbt package (e.g., stripe/hubspot)
3. **MAR optimization** — Audit current usage, reduce MAR by 30%
4. **Custom connector** — Build Lambda function connector cho internal API

### Resources

- 📖 [dbt Integration](https://fivetran.com/docs/transformations/dbt)
- 📖 [Quickstart Packages](https://fivetran.com/docs/transformations/dbt/quickstart)
- 📖 [Fivetran Functions](https://fivetran.com/docs/connectors/connector-sdk)
- 📖 [Pricing & MAR](https://fivetran.com/docs/getting-started/consumption-based-pricing)

---

## Level 3: Advanced (4–8 tuần)

### HVR (High-Volume Replication)

```
Fivetran HVR = enterprise-grade CDC for:
- Oracle → Snowflake/Databricks
- SQL Server → BigQuery
- SAP HANA → any destination
- High-throughput (millions of rows/hour)

Features:
- Log-based CDC (minimal source impact)
- Real-time replication (sub-second latency)
- Initial bulk load + continuous sync
- Schema drift handling
- Data validation

Configuration:
1. Fivetran Dashboard > HVR connector
2. Install HVR agent on source network
3. Configure channels (source → destination mapping)
4. Enable log-based capture
5. Monitor replication lag
```

### Private Networking

```
Enterprise networking options:

1. AWS PrivateLink:
   - Fivetran connects to your VPC privately
   - No public internet exposure
   - Setup: VPC Endpoint Service → share with Fivetran

2. SSH Tunnel:
   - Bastion host in your VPC
   - Fivetran → SSH → private database
   - Public key authentication

3. VPN:
   - IPSec VPN between Fivetran and your network
   - For on-premise databases

4. Reverse SSH Tunnel:
   - Agent in your network connects outbound to Fivetran
   - No inbound firewall rules needed
```

### Metadata API

```python
import requests

FIVETRAN_API_KEY = "your-api-key"
FIVETRAN_API_SECRET = "your-api-secret"

BASE_URL = "https://api.fivetran.com/v1"
auth = (FIVETRAN_API_KEY, FIVETRAN_API_SECRET)

# List all connectors
connectors = requests.get(
    f"{BASE_URL}/groups/group_id/connectors",
    auth=auth
).json()

for connector in connectors["data"]["items"]:
    print(f"{connector['schema']}: {connector['status']['sync_state']}")

# Get connector sync status
status = requests.get(
    f"{BASE_URL}/connectors/{connector_id}",
    auth=auth
).json()

print(f"Last sync: {status['data']['succeeded_at']}")
print(f"Status: {status['data']['status']['sync_state']}")

# Trigger manual sync
requests.post(
    f"{BASE_URL}/connectors/{connector_id}/force",
    auth=auth
)

# Pause/resume connector
requests.patch(
    f"{BASE_URL}/connectors/{connector_id}",
    auth=auth,
    json={"paused": True}
)

# Get MAR usage
usage = requests.get(
    f"{BASE_URL}/groups/group_id/usage",
    auth=auth
).json()
```

### Fivetran Lite (Self-managed)

```
Fivetran Lite = self-managed deployment trong your cloud:
- Data never leaves your VPC
- Same connectors as cloud Fivetran
- Deployed on your Kubernetes cluster
- Compliance: data residency, SOC2, HIPAA

Setup:
1. Get Fivetran Lite license
2. Deploy agent in your K8s cluster
3. Configure via Fivetran dashboard (control plane)
4. Data plane runs entirely in your infrastructure
```

### Bài tập

1. **Metadata API** — Script monitor tất cả connectors, alert on failures
2. **Private networking** — Setup SSH tunnel connector cho private database
3. **Multi-destination** — Replicate same source to 2 destinations
4. **Disaster recovery** — Design DR strategy với Fivetran (re-sync, history mode)

### Resources

- 📖 [Fivetran REST API](https://fivetran.com/docs/rest-api)
- 📖 [Private Networking](https://fivetran.com/docs/databases/connection-options)
- 📖 [HVR Documentation](https://fivetran.com/docs/hvr)
- 📖 [Fivetran Lite](https://fivetran.com/docs/lite)
- 📖 [Security & Compliance](https://fivetran.com/docs/security)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **Fivetran** | Managed SaaS | Zero-maintenance, 300+ connectors, reliable | Expensive at scale, vendor lock-in |
| **Airbyte** | Open-source | Free, self-host, CDC, extensible | Resource-heavy, maintenance needed |
| **dlt** | Python library | Pip install, lightweight, flexible | No UI, DIY maintenance |
| **Stitch** | Managed SaaS | Simple, affordable, Singer-based | Limited connectors, basic features |
| **Hevo** | Managed SaaS | No-code, good UI, affordable | Fewer connectors, less mature |
| **Meltano** | Open-source CLI | GitOps, Singer ecosystem, free | CLI-only, smaller community |

### Khi nào chọn gì?

- **Fivetran** → Enterprise needing reliability, zero maintenance, budget available
- **Airbyte** → Self-hosted preference, open-source, CDC needs, cost-sensitive
- **dlt** → Python devs, lightweight, serverless deployment, custom sources
- **Stitch** → Small/medium team, moderate budget, simple needs
- **Hevo** → No-code preference, moderate budget, quick setup
- **Meltano** → GitOps workflow, developer-centric, Singer ecosystem
