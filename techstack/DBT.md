# 🔧 dbt (Data Build Tool) — Learning Guide (Beginner → Advanced)

## Tổng quan

dbt là transformation framework cho analytics. Biến raw data → trusted models bằng SQL + software engineering practices (testing, docs, CI/CD, version control).

**Dùng cho:** AE (core tool), BI Engineer, DE

---

## Level 1: Beginner (2 tuần)

### Core Concepts
- Model = SQL SELECT statement → materialized thành table/view
- `ref()` = reference between models (builds DAG)
- `source()` = reference raw data
- Materializations: view, table, incremental, ephemeral
- Project structure: models/, tests/, macros/, seeds/

### Project Structure
```
dbt_project/
├── dbt_project.yml          # Project config
├── profiles.yml             # Connection config
├── models/
│   ├── staging/             # stg_ models (clean raw)
│   │   ├── stg_customers.sql
│   │   └── _stg_schema.yml
│   ├── intermediate/        # int_ models (business logic)
│   └── marts/               # final models (consumption)
│       ├── customers.sql
│       └── _marts_schema.yml
├── tests/                   # Custom data tests
├── macros/                  # Reusable SQL snippets
├── seeds/                   # Static CSV data
└── snapshots/               # SCD Type 2
```

### First Models
```sql
-- models/staging/stg_customers.sql
WITH source AS (
    SELECT * FROM {{ source('raw', 'customers') }}
),
renamed AS (
    SELECT
        id AS customer_id,
        TRIM(name) AS customer_name,
        email,
        created_at::DATE AS signup_date
    FROM source
    WHERE id IS NOT NULL
)
SELECT * FROM renamed
```

```sql
-- models/marts/customers.sql
WITH customers AS (
    SELECT * FROM {{ ref('stg_customers') }}
),
orders AS (
    SELECT * FROM {{ ref('stg_orders') }}
),
final AS (
    SELECT
        c.customer_id,
        c.customer_name,
        COUNT(o.order_id) AS total_orders,
        SUM(o.amount) AS lifetime_value
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY 1, 2
)
SELECT * FROM final
```

### Essential Commands
```bash
dbt run                          # Run all models
dbt run -s stg_customers         # Run specific model
dbt run -s +stg_customers        # Run model + upstream
dbt run -s stg_customers+        # Run model + downstream
dbt test                         # Run all tests
dbt docs generate && dbt docs serve  # Generate docs
dbt compile                      # Compile SQL (no execute)
```

### Bài tập
1. Setup dbt project (use dbt init)
2. Create 3 staging models from source
3. Create 1 mart model using ref()
4. Add source.yml with freshness check
5. Run `dbt docs generate` and explore DAG

### Resources
| Resource | Link | Type |
|---|---|---|
| dbt Fundamentals (official) | [courses.getdbt.com](https://courses.getdbt.com/courses/fundamentals) | Free |
| Jaffle Shop Tutorial | [github.com/dbt-labs/jaffle_shop](https://github.com/dbt-labs/jaffle_shop) | Free |
| dbt Best Practices | [docs.getdbt.com/best-practices](https://docs.getdbt.com/best-practices) | Free |
| dbt Docs | [docs.getdbt.com](https://docs.getdbt.com/) | Free |

---

## Level 2: Intermediate (4 tuần)

### Testing
```yaml
# _schema.yml
version: 2
models:
  - name: stg_customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null
      - name: email
        tests:
          - unique
          - not_null
      - name: status
        tests:
          - accepted_values:
              values: ['active', 'inactive', 'churned']
```

### Incremental Models
```sql
-- models/golden/ev_transactions__fa.sql
{{
    config(
        materialized='incremental',
        incremental_strategy='append',
        unique_key='transaction_id'
    )
}}

SELECT
    transaction_id,
    customer_id,
    amount,
    transaction_date,
    '{{ var("business_date") }}'::DATE AS partition_date
FROM {{ source('raw', 'transactions') }}

{% if is_incremental() %}
WHERE transaction_date > (SELECT MAX(transaction_date) FROM {{ this }})
{% endif %}
```

### Sources & Freshness
```yaml
# models/staging/_sources.yml
version: 2
sources:
  - name: raw
    database: "{{ var('database') }}"
    schema: raw_data
    freshness:
      warn_after: {count: 12, period: hour}
      error_after: {count: 24, period: hour}
    loaded_at_field: _loaded_at
    tables:
      - name: customers
      - name: orders
      - name: transactions
```

### Macros (Jinja)
```sql
-- macros/generate_schema_name.sql
{% macro generate_schema_name(custom_schema_name, node) %}
    {{ custom_schema_name | trim }}
{% endmacro %}

-- macros/cents_to_dollars.sql
{% macro cents_to_dollars(column_name) %}
    ({{ column_name }} / 100.0)::DECIMAL(18,2)
{% endmacro %}

-- Usage in model:
SELECT {{ cents_to_dollars('amount_cents') }} AS amount
```

### dbt Packages
```yaml
# packages.yml
packages:
  - package: dbt-labs/dbt_utils
    version: [">=1.0.0", "<2.0.0"]
  - package: calogica/dbt_expectations
    version: [">=0.10.0", "<1.0.0"]
```

### Bài tập
1. Convert 3 models to incremental (append strategy)
2. Write 20+ tests across project
3. Create 2 custom macros
4. Add dbt_utils: surrogate_key, date_spine
5. Implement source freshness checks

### Resources
| Resource | Link | Type |
|---|---|---|
| dbt Advanced Materializations | [courses.getdbt.com](https://courses.getdbt.com/courses/advanced-materializations) | Free |
| Jinja, Macros, Packages | [courses.getdbt.com](https://courses.getdbt.com/courses/jinja-macros-packages) | Free |
| dbt-utils package | [hub.getdbt.com/dbt-labs/dbt_utils](https://hub.getdbt.com/dbt-labs/dbt_utils/latest/) | Free |
| dbt-expectations | [github.com/calogica/dbt-expectations](https://github.com/calogica/dbt-expectations) | Free |

---

## Level 3: Advanced (6 tuần)

### Custom Materializations
```sql
-- macros/materializations/incremental_append.sql
{% materialization incremental_append, adapter='redshift' %}
    -- Custom logic for append strategy
{% endmaterialization %}
```

### Slim CI / Deferred Runs
```bash
# Only run/test modified models + downstream
dbt run -s state:modified+ --defer --state ./prod-manifest
dbt test -s state:modified+ --defer --state ./prod-manifest

# Defer to UAT state
dbt run -s model --target dev --defer --favor-state --state uat
```

### MetricFlow / Semantic Layer
```yaml
# models/metrics/revenue.yml
semantic_models:
  - name: orders
    defaults:
      agg_time_dimension: order_date
    entities:
      - name: order_id
        type: primary
      - name: customer_id
        type: foreign
    dimensions:
      - name: order_date
        type: time
    measures:
      - name: revenue
        agg: sum
        expr: amount

metrics:
  - name: total_revenue
    type: simple
    type_params:
      measure: revenue
```

### Advanced Patterns
```sql
-- SCD Type 2 with snapshots
-- snapshots/customer_snapshot.sql
{% snapshot customer_snapshot %}
{{
    config(
        target_schema='golden',
        unique_key='customer_id',
        strategy='timestamp',
        updated_at='updated_at'
    )
}}
SELECT * FROM {{ source('raw', 'customers') }}
{% endsnapshot %}

-- Dynamic model generation (codegen)
-- Generate staging models automatically from source schema
```

### Orchestration Integration (Airflow + Cosmos)
```python
# Cosmos integration
from cosmos import DbtTaskGroup, ProjectConfig, ProfileConfig

dbt_tasks = DbtTaskGroup(
    project_config=ProjectConfig(dbt_project_path="/dbt/project"),
    profile_config=ProfileConfig(
        profile_name="my_project",
        target_name="dev"
    ),
    operator_args={"vars": '{"business_date": "{{ ds }}"}'}
)
```

### Data Contracts
```yaml
# models/contracts/customer_contract.yml
models:
  - name: dim_customer
    config:
      contract:
        enforced: true
    columns:
      - name: customer_id
        data_type: bigint
        constraints:
          - type: not_null
          - type: primary_key
      - name: customer_name
        data_type: varchar(200)
```

### Bài tập
1. Implement slim CI pipeline (GitHub Actions / GitLab CI)
2. Create MetricFlow semantic model
3. Build snapshot (SCD2) for customer dimension
4. Configure Cosmos DAG for dbt project
5. Write custom materialization

### Resources
| Resource | Link | Type |
|---|---|---|
| dbt Certification prep | [getdbt.com/certifications](https://www.getdbt.com/certifications/) | Paid |
| MetricFlow docs | [docs.getdbt.com/docs/build/about-metricflow](https://docs.getdbt.com/docs/build/about-metricflow) | Free |
| Astronomer Cosmos | [github.com/astronomer/astronomer-cosmos](https://github.com/astronomer/astronomer-cosmos) | Free |
| Analytics Engineering with SQL and dbt (book) | Rui Machado | Paid |
| dbt Community Slack | [community.getdbt.com](https://community.getdbt.com/) | Free |

---
## Alternatives & Công cụ tương đương

| Tool | Description | Comparison with dbt |
|---|---|---|
| **SQLMesh** | Open-source, built-in CI, column-level lineage | More opinionated, Python-native, virtual environments |
| **Dataform** | Google Cloud native, SQL + JS | Integrated with BigQuery, less flexible than dbt |
| **dlt (data load tool)** | Python-first ELT | Focus on loading, not transformation |
| **Coalesce** | Visual UI for transformations | Low-code, Snowflake-native |
| **Matillion** | ETL/ELT platform (GUI) | Enterprise, less code-first |
| **stored procedures** | Traditional approach | No version control, testing, docs |

### Khi nào chọn gì?
- **dbt** → Industry standard, largest community, most jobs
- **SQLMesh** → Want better CI/CD built-in, column lineage
- **Dataform** → All-in on Google Cloud / BigQuery
- **Coalesce** → Team prefers visual/low-code + Snowflake
