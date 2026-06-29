# 🔴 Amazon Redshift — Learning Guide (Beginner → Advanced)

## Tổng quan

Amazon Redshift là cloud data warehouse (columnar, MPP). Optimized cho analytics queries trên large datasets (TB-PB scale).

**Dùng cho:** AE, BI Eng, DG (RBAC), DA (query)

---

## Level 1: Beginner (1 tuần)

### Core Concepts
- **Columnar storage**: data lưu theo cột (tốt cho analytics)
- **MPP** (Massively Parallel Processing): queries chạy trên nhiều nodes
- **Leader node + Compute nodes**: leader parse/plan, compute execute
- **Distribution**: cách data phân bổ across nodes
- **Sort key**: cách data sắp xếp trên disk

### Basic Querying
```sql
-- Connect via DBeaver/SQL Workbench
-- Same syntax as PostgreSQL (mostly)

SELECT current_user;
SELECT current_database();

-- System tables
SELECT * FROM svv_table_info WHERE schema = 'golden' LIMIT 10;
SELECT * FROM stl_query WHERE userid > 1 ORDER BY starttime DESC LIMIT 20;

-- Basic query (same as PostgreSQL)
SELECT customer_id, COUNT(*) AS order_count
FROM golden.orders
WHERE order_date = '2024-06-01'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

### Bài tập
1. Connect to Redshift via DBeaver
2. Explore schemas: list tables, column types
3. Write 5 basic queries on golden layer tables
4. Check table sizes with svv_table_info

### Resources
| Resource | Link | Type |
|---|---|---|
| Redshift Getting Started | [AWS Docs](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html) | Free |
| Redshift SQL Reference | [AWS Docs](https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_commands.html) | Free |

---

## Level 2: Intermediate (3 tuần)

### Distribution Styles
```sql
-- EVEN: spread equally (default, good for small dims)
CREATE TABLE dim_region (...) DISTSTYLE EVEN;

-- KEY: co-locate rows with same key (good for JOINs)
CREATE TABLE fact_transactions (
    customer_id BIGINT,
    ...
) DISTSTYLE KEY DISTKEY (customer_id);

-- ALL: full copy on each node (small dims < 3M rows)
CREATE TABLE dim_product (...) DISTSTYLE ALL;

-- AUTO: Redshift decides (recommended for new tables)
CREATE TABLE my_table (...) DISTSTYLE AUTO;
```

### Sort Keys
```sql
-- Compound sort key (filter by leading columns)
CREATE TABLE fact_transactions (...)
SORTKEY (transaction_date, customer_id);

-- Interleaved sort key (equal weight, multiple filter patterns)
CREATE TABLE events (...)
INTERLEAVED SORTKEY (event_date, user_id, event_type);
```

### Query Performance
```sql
-- EXPLAIN to check query plan
EXPLAIN
SELECT c.name, SUM(t.amount)
FROM golden.customer c
JOIN golden.transactions t ON c.customer_id = t.customer_id
WHERE t.transaction_date >= '2024-01-01'
GROUP BY 1;

-- Look for:
-- DS_DIST_NONE = co-located (good!)
-- DS_BCAST_INNER = broadcast (bad for large tables)
-- DS_DIST_ALL_NONE = ALL distribution used (good)

-- Check recent slow queries
SELECT query, elapsed/1000000 AS seconds, substring
FROM svl_qlog
WHERE elapsed > 5000000  -- > 5 seconds
ORDER BY elapsed DESC LIMIT 20;

-- Table statistics
ANALYZE golden.customer;
VACUUM golden.customer;
```

### RBAC (Access Control)
```sql
-- Example: 12 roles defined
-- View current grants
SELECT * FROM svv_relation_privileges
WHERE identity_name = 'role_da_read';

-- Grant patterns
GRANT USAGE ON SCHEMA golden TO ROLE role_da_read;
GRANT SELECT ON ALL TABLES IN SCHEMA golden TO ROLE role_da_read;

-- Row-Level Security (RLS)
CREATE RLS POLICY branch_filter
WITH (branch_code VARCHAR(10))
USING (branch_code = current_setting('app.branch_code'));
```

### Bài tập
1. Check distribution style of 5 tables
2. Write EXPLAIN for 3 complex queries, identify bottlenecks
3. Design dist/sort keys for a new fact table
4. Run ANALYZE + VACUUM on a table, measure impact
5. Query system tables for performance insights

### Resources
| Resource | Link | Type |
|---|---|---|
| Redshift Table Design | [AWS Docs](https://docs.aws.amazon.com/redshift/latest/dg/c_designing-tables-best-practices.html) | Free |
| Redshift Performance Tuning | [AWS Docs](https://docs.aws.amazon.com/redshift/latest/dg/c-optimizing-query-performance.html) | Free |
| AWS Redshift Immersion Day | [Workshop](https://catalog.workshops.aws/redshift-immersion/en-US) | Free |

---

## Level 3: Advanced (4 tuần)

### Workload Management (WLM)
```sql
-- Check WLM queues
SELECT * FROM stv_wlm_query_state;

-- Concurrency scaling (auto-scale for burst)
-- Configured in console, not SQL

-- Query priorities
SET query_group TO 'high_priority';
SELECT ... ;
RESET query_group;
```

### Advanced Features
```sql
-- Materialized Views
CREATE MATERIALIZED VIEW mv_daily_revenue AS
SELECT transaction_date, SUM(amount) AS total_revenue
FROM golden.transactions
GROUP BY 1;

REFRESH MATERIALIZED VIEW mv_daily_revenue;

-- Spectrum (query S3 directly)
CREATE EXTERNAL SCHEMA spectrum_schema
FROM DATA CATALOG
DATABASE 'my_glue_db'
IAM_ROLE 'arn:aws:iam::123456:role/RedshiftSpectrumRole';

-- Data Sharing (cross-account)
CREATE DATASHARE my_share;
ALTER DATASHARE my_share ADD SCHEMA platinum;

-- Federated Query (RDS/Aurora)
CREATE EXTERNAL SCHEMA rds_schema
FROM POSTGRES DATABASE 'mydb'
URI 'rds-endpoint'
IAM_ROLE 'arn:aws:iam::...'
SECRET_ARN 'arn:aws:secretsmanager::...';
```

### Cost Optimization
```sql
-- Check storage usage
SELECT "table", size AS mb, pct_used
FROM svv_table_info
WHERE schema = 'golden'
ORDER BY size DESC LIMIT 20;

-- Compression analysis
ANALYZE COMPRESSION golden.fact_transactions;

-- Short query acceleration (SQA)
-- Auto-route short queries to fast lane

-- Concurrency scaling: only pay for burst
-- Reserved vs on-demand nodes
```

### Monitoring & Observability
```sql
-- Query execution details
SELECT query, segment, step, label, rows, bytes
FROM svl_query_summary
WHERE query = 12345
ORDER BY segment, step;

-- Disk-based queries (bad: spilling to disk)
SELECT query, segment, step, workmem, rows
FROM svl_query_report
WHERE is_diskbased = 't';

-- Lock monitoring
SELECT * FROM svv_transactions WHERE lock_mode IS NOT NULL;
```

### Bài tập
1. Create materialized view + refresh strategy
2. Analyze compression types for 5 columns
3. Design WLM queue configuration for mixed workload
4. Calculate storage cost optimization (compress + vacuum)
5. Build monitoring query dashboard

### Resources
| Resource | Link | Type |
|---|---|---|
| Redshift Advanced Queries | [AWS re:Post](https://repost.aws/tags/TAkLB6lFupRCi2NnrI9GJRzg/amazon-redshift) | Free |
| AWS Data Analytics Specialty | [AWS Certification](https://aws.amazon.com/certification/certified-data-analytics-specialty/) | Paid |
| Redshift System Tables Reference | [AWS Docs](https://docs.aws.amazon.com/redshift/latest/dg/cm_chap_system-tables.html) | Free |

---
## Alternatives & Công cụ tương đương

| Tool | Description | vs Redshift |
|---|---|---|
| **Snowflake** | Multi-cloud, instant scaling | More flexible, higher cost |
| **BigQuery** | Serverless, Google ecosystem | No cluster management, per-query pricing |
| **Databricks SQL** | Lakehouse, Spark-based | Unified analytics + ML |
| **Azure Synapse** | Microsoft ecosystem | Integrated with Power BI |
| **ClickHouse** | Open-source, real-time analytics | Fast for OLAP, self-managed |
| **DuckDB** | Embedded, local analytics | Single-machine, no infra needed |

### Khi nào chọn gì?
- **Redshift** → AWS-heavy org, banking/enterprise
- **Snowflake** → Multi-cloud, need auto-scaling
- **BigQuery** → GCP org, want serverless
- **Databricks** → Need ML + analytics unified
