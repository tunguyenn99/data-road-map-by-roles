# 📖 OpenMetadata — Learning Guide (Beginner → Advanced)

## Tổng quan

OpenMetadata là open-source data catalog & governance platform. Manage metadata, lineage, data quality, ownership, classification, và collaboration across data assets.

**Dùng cho:** DG (primary), AE (lineage, docs)

---

## Level 1: Beginner (1 tuần)

### Core Concepts
- **Services**: data sources (Redshift, Airflow, dbt...)
- **Tables/Topics/Dashboards**: cataloged assets
- **Metadata**: descriptions, tags, owners, tier
- **Lineage**: data flow visualization
- **Teams & Users**: organizational hierarchy
- **Glossary**: business term definitions

### UI Navigation
1. **Explore**: search & browse all data assets
2. **Data Quality**: test results, profiling
3. **Lineage**: visual DAG of data flow
4. **Glossary**: business terms + definitions
5. **Teams**: organizational structure
6. **Settings**: services, policies, roles

### First Tasks
- Browse existing tables, read descriptions
- Search for a table by name
- View lineage of a specific model
- Check data quality test results
- Find table owner

### Bài tập
1. Search 5 tables, note their owners and descriptions
2. Trace lineage from source → staging → golden → platinum
3. Find all tables tagged with "PII"
4. View glossary terms
5. Identify tables without descriptions

### Resources
| Resource | Link | Type |
|---|---|---|
| OpenMetadata Docs | [docs.open-metadata.org](https://docs.open-metadata.org/) | Free |
| OpenMetadata GitHub | [github.com/open-metadata](https://github.com/open-metadata/OpenMetadata) | Free |
| YouTube Tutorials | [youtube.com/@OpenMetadata](https://www.youtube.com/@OpenMetadata) | Free |

---

## Level 2: Intermediate (2 tuần)

### Metadata Curation
```yaml
# Add descriptions, tags, owners via UI or API
# Table-level: description, owner, tier, tags
# Column-level: description, glossary term, classification

# Classification tags:
# - PII (Personally Identifiable Information)
# - PHI (Protected Health Information)
# - Sensitive
# - Internal / Confidential / Public
```

### API Usage (Python)
```python
import requests

BASE_URL = "https://openmetadata.your-company.cloud/api"
HEADERS = {"Authorization": f"Bearer {token}"}

# Search tables
response = requests.get(
    f"{BASE_URL}/v1/tables",
    params={"database": "analytics_db.golden", "limit": 50},
    headers=HEADERS
)
tables = response.json()["data"]

# Get table details
table = requests.get(
    f"{BASE_URL}/v1/tables/name/analytics_db.golden.customer",
    params={"fields": "columns,owner,tags,lineage"},
    headers=HEADERS
).json()

# Update description
requests.patch(
    f"{BASE_URL}/v1/tables/{table_id}",
    json=[{"op": "add", "path": "/description", "value": "Customer master data"}],
    headers={**HEADERS, "If-Match": etag}  # ETag required!
)
```

### Lineage
```python
# View lineage via API
lineage = requests.get(
    f"{BASE_URL}/v1/lineage/table/name/{fqn}",
    params={"upstreamDepth": 3, "downstreamDepth": 3},
    headers=HEADERS
).json()

# dbt auto-generates lineage via metadata ingestion
# Airflow lineage comes via Airflow provider
```

### Data Quality (via dbt integration)
- dbt tests results → ingested into OpenMetadata
- Column-level profiling: null%, unique%, distribution
- Custom quality tests definable in UI

### Bài tập
1. Use API to list all tables in golden schema
2. Add descriptions to 10 tables via API
3. Tag 5 columns as PII
4. Create glossary term: "Customer LTV"
5. View lineage depth 3 upstream/downstream

### Resources
| Resource | Link | Type |
|---|---|---|
| OpenMetadata API Reference | [docs.open-metadata.org/swagger](https://docs.open-metadata.org/swagger.html) | Free |
| OpenMetadata Python SDK | [pypi.org/project/openmetadata-ingestion](https://pypi.org/project/openmetadata-ingestion/) | Free |
| Atlan Community (similar concepts) | [community.atlan.com](https://community.atlan.com/) | Free |

---

## Level 3: Advanced (3 tuần)

### Service Configuration
```yaml
# Register Redshift service
service_config:
  type: Redshift
  connection:
    hostPort: "redshift-cluster.amazonaws.com:5439"
    database: analytics_db
    username: "{{ env_var('OPENMETADATA_REDSHIFT_USERNAME') }}"
    password: "{{ env_var('OPENMETADATA_REDSHIFT_PASSWORD') }}"
```

### Roles & Policies
```python
# Custom roles example:
# - DataSteward: manage tags, descriptions, ownership
# - DataConsumer: read-only browse
# - Admin: full access

# Policy rules define what each role can do
# Based on: resource type, tags, ownership, team membership
```

### Automated Ingestion
```yaml
# Ingestion pipeline configs:
# 1. Metadata: table schemas, columns, constraints
# 2. Lineage: dbt manifest → OpenMetadata lineage
# 3. Profiler: column statistics, distributions
# 4. Usage: query frequency from Redshift query logs
# 5. dbt: models, tests, docs

# Schedule: daily after dbt pipeline completes
```

### Custom Classification & Automation
```python
# Auto-classify PII columns by pattern
PII_PATTERNS = {
    "email": r".*email.*|.*e_mail.*",
    "phone": r".*phone.*|.*mobile.*|.*tel.*",
    "id_number": r".*cccd.*|.*cmnd.*|.*id_number.*",
    "address": r".*address.*|.*dia_chi.*",
}

# Scan all columns, auto-tag matching patterns
for table in tables:
    for column in table["columns"]:
        for pii_type, pattern in PII_PATTERNS.items():
            if re.match(pattern, column["name"], re.IGNORECASE):
                tag_column_as_pii(table, column, pii_type)
```

### Data Governance Framework
```
OpenMetadata enables:
├── Discovery: search, browse, lineage
├── Classification: PII tags, sensitivity levels
├── Ownership: team/person responsible
├── Quality: profiling, test results
├── Collaboration: conversations, tasks
└── Compliance: audit trail, access policies
```

### Bài tập
1. Configure Redshift service ingestion
2. Set up automated PII classification script
3. Create custom roles & policies
4. Build data quality pipeline integration
5. Design governance dashboard (completeness metrics)

### Resources
| Resource | Link | Type |
|---|---|---|
| OpenMetadata Deployment | [docs.open-metadata.org/deployment](https://docs.open-metadata.org/deployment) | Free |
| OpenMetadata Connectors | [docs.open-metadata.org/connectors](https://docs.open-metadata.org/connectors) | Free |

---
## Alternatives & Công cụ tương đương

| Tool | Type | vs OpenMetadata |
|---|---|---|
| **Atlan** | Commercial SaaS | More polished UI, expensive |
| **Collibra** | Enterprise commercial | Most mature, very expensive |
| **DataHub (LinkedIn)** | Open-source | Strong lineage, complex setup |
| **Amundsen (Lyft)** | Open-source | Discovery-focused, less governance |
| **Alation** | Commercial | Strong catalog, AI-powered |
| **Marquez** | Open-source (lineage only) | Lightweight, lineage-focused |
| **dbt Docs** | Free (built-in) | Basic catalog, lineage from dbt |

### Khi nào chọn gì?
- **OpenMetadata** → Open-source, growing fast, good all-rounder
- **Atlan** → Budget available, want polished UX
- **Collibra** → Large enterprise, heavy compliance needs
- **DataHub** → Engineering-heavy team, complex lineage
- **dbt Docs** → Already using dbt, basic needs
