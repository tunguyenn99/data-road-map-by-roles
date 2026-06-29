# ☁️ Cloud Data Warehouses (Snowflake & BigQuery) — Learning Guide

## Tổng quan

Ngoài Redshift (xem [REDSHIFT.md](./REDSHIFT.md)), hai cloud DW phổ biến khác là **Snowflake** và **Google BigQuery**. Hiểu cả 3 giúp linh hoạt trong career.

**Dùng cho:** AE, BI Engineer, DE

---

## Snowflake

### Level 1: Beginner (1 tuần)

**Core Concepts:**
- Virtual Warehouses (compute) tách biệt Storage
- Databases → Schemas → Tables/Views
- Time Travel & Zero-Copy Clone
- Credits-based pricing (pay for compute)

```sql
-- Basic queries (ANSI SQL)
SELECT * FROM my_db.my_schema.customers LIMIT 10;

-- Warehouse management
USE WAREHOUSE analytics_wh;
ALTER WAREHOUSE analytics_wh SET WAREHOUSE_SIZE = 'MEDIUM';

-- Time Travel
SELECT * FROM customers AT(OFFSET => -3600);  -- 1 hour ago
SELECT * FROM customers BEFORE(TIMESTAMP => '2024-01-01');

-- Clone (zero-copy, instant)
CREATE TABLE customers_backup CLONE customers;
CREATE DATABASE dev_db CLONE prod_db;
```

### Level 2: Intermediate (2 tuần)

```sql
-- Clustering (auto or manual)
ALTER TABLE fact_orders CLUSTER BY (order_date, customer_id);

-- Streams (Change Data Capture)
CREATE STREAM orders_stream ON TABLE raw_orders;
SELECT * FROM orders_stream;  -- Shows inserts/updates/deletes

-- Tasks (scheduling)
CREATE TASK daily_transform
  WAREHOUSE = transform_wh
  SCHEDULE = 'USING CRON 0 8 * * * UTC'
AS
  INSERT INTO mart_daily SELECT ... FROM orders_stream;

-- Materialized Views
CREATE MATERIALIZED VIEW mv_daily_sales AS
SELECT date, SUM(amount) AS revenue FROM orders GROUP BY date;

-- Data Sharing (cross-account, no copy)
CREATE SHARE analytics_share;
GRANT USAGE ON DATABASE analytics_db TO SHARE analytics_share;
```

### Level 3: Advanced (3 tuần)
- Snowpark (Python/Java/Scala on Snowflake)
- Dynamic Tables (auto-refresh materialized views)
- Snowpipe (continuous loading from S3/GCS/Azure)
- Cost governance: resource monitors, query tags
- Multi-cluster warehouses (auto-scale)

### Certification
- **SnowPro Core** — [snowflake.com/certifications](https://www.snowflake.com/certifications/)

---

## Google BigQuery

### Level 1: Beginner (1 tuần)

**Core Concepts:**
- Serverless (no infrastructure to manage)
- Projects → Datasets → Tables
- Columnar storage (Capacitor format)
- Pricing: on-demand (per TB scanned) or slots (reserved)
- Nested/repeated fields (STRUCT, ARRAY)

```sql
-- Basic queries
SELECT * FROM `project.dataset.table` LIMIT 10;

-- Public datasets (free to query)
SELECT name, SUM(number) AS total
FROM `bigquery-public-data.usa_names.usa_1910_2013`
GROUP BY name
ORDER BY total DESC LIMIT 10;

-- Partitioned tables
SELECT * FROM `my_project.sales.transactions`
WHERE DATE(transaction_date) = '2024-06-01';  -- Only scans 1 partition

-- Wildcard tables
SELECT * FROM `my_project.logs.events_*`
WHERE _TABLE_SUFFIX BETWEEN '20240101' AND '20240131';
```

### Level 2: Intermediate (2 tuần)

```sql
-- Partitioning
CREATE TABLE my_dataset.orders
PARTITION BY DATE(order_date)
CLUSTER BY customer_id, product_id
AS SELECT * FROM raw_orders;

-- Nested fields (STRUCT)
SELECT
  customer.name,
  customer.address.city,
  order_item.product_name
FROM `orders`,
UNNEST(items) AS order_item;

-- JavaScript UDFs
CREATE FUNCTION my_dataset.clean_phone(phone STRING)
RETURNS STRING
LANGUAGE js AS """
  return phone.replace(/[^0-9]/g, '');
""";

-- Scheduled queries
-- Via BigQuery Console or API

-- Materialized Views
CREATE MATERIALIZED VIEW my_dataset.mv_daily AS
SELECT DATE(created_at) AS date, COUNT(*) AS events
FROM my_dataset.raw_events
GROUP BY 1;
```

### Level 3: Advanced (3 tuần)
- BigQuery ML (train models in SQL)
- BigQuery BI Engine (in-memory acceleration)
- Remote functions (Cloud Functions integration)
- Data Transfer Service (scheduled loads)
- Authorized views & row-level security
- Cost optimization: slot reservations, BI Engine, caching

### Certification
- **Google Professional Data Engineer** — [cloud.google.com/certification](https://cloud.google.com/certification/data-engineer)

---

## So sánh 3 Cloud DW

| Feature | Redshift | Snowflake | BigQuery |
|---|---|---|---|
| **Pricing model** | Per-node (hourly) | Per-credit (compute) | Per-TB scanned / Slots |
| **Scaling** | Resize cluster | Instant (virtual WH) | Auto (serverless) |
| **Storage/Compute** | Coupled (RA3 decoupled) | Decoupled | Decoupled |
| **Concurrency** | WLM queues | Multi-cluster WH | Auto |
| **Semi-structured** | SUPER type | VARIANT | STRUCT/ARRAY |
| **Time Travel** | No (use snapshots) | Yes (up to 90 days) | Yes (7 days) |
| **Clone** | No | Zero-copy | Snapshot copy |
| **ML built-in** | Redshift ML | Snowpark ML | BigQuery ML |
| **Best for** | AWS-heavy, banking | Multi-cloud, flexibility | GCP-heavy, serverless |
| **VN Market** | Banking (VPBank, Techcombank) | Growing (startups) | E-commerce (Shopee, Grab) |

---

## Resources

| Resource | Link | Type |
|---|---|---|
| Snowflake Hands-on Lab | [quickstarts.snowflake.com](https://quickstarts.snowflake.com/) | Free |
| BigQuery Sandbox (free) | [cloud.google.com/bigquery/docs/sandbox](https://cloud.google.com/bigquery/docs/sandbox) | Free |
| Snowflake University | [learn.snowflake.com](https://learn.snowflake.com/) | Free |
| Google Cloud Skills Boost | [cloudskillsboost.google](https://www.cloudskillsboost.google/) | Free/Paid |
| SnowPro Core Study Guide | [snowflake.com](https://www.snowflake.com/certifications/) | Free |

---
## Alternatives & Công cụ tương đương

| Category | Tools | Use case |
|---|---|---|
| Cloud DW | Redshift, Snowflake, BigQuery, Databricks, Synapse | Enterprise analytics |
| Open-source DW | ClickHouse, Apache Druid, StarRocks | Real-time analytics, cost control |
| Lakehouse | Databricks, Apache Iceberg + Spark, Delta Lake | ML + analytics unified |
| Embedded/Local | DuckDB, SQLite | Prototyping, edge analytics |
| Real-time | Apache Kafka + ksqlDB, Materialize | Streaming analytics |
