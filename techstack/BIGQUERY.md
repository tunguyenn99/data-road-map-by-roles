# 🔷 BigQuery — Learning Guide (Beginner → Advanced)

## Tổng quan

**Google BigQuery** là serverless data warehouse trên Google Cloud Platform. Bạn không cần quản lý infrastructure — chỉ cần write SQL và trả tiền theo data scanned (on-demand) hoặc reserved slots.

**Use cases:**
- Analytics data warehouse (petabyte-scale)
- Ad-hoc analysis trên large datasets
- ML training trực tiếp bằng SQL (BigQuery ML)
- Real-time analytics với streaming inserts

**Ai dùng:** Data Engineers, Analytics Engineers, Data Analysts, Data Scientists

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Project** | Top-level container (billing, IAM) |
| **Dataset** | Collection of tables (like schema) |
| **Table** | Structured data storage |
| **View** | Saved query (logical, not stored) |
| **Partitioning** | Chia table theo column (date, integer range) |
| **Clustering** | Sort data within partitions cho faster queries |
| **Slots** | Units of computational capacity |
| **On-demand** | Pay per TB scanned |

### Setup — Free Sandbox

```sql
-- BigQuery Sandbox: 10 GB storage free, 1 TB queries/month free
-- Go to: https://console.cloud.google.com/bigquery
-- No credit card needed for sandbox

-- Query public datasets
SELECT
  name,
  number,
  state
FROM `bigquery-public-data.usa_names.usa_1910_current`
WHERE year = 2020
ORDER BY number DESC
LIMIT 10;
```

### First Queries

```sql
-- Create dataset
CREATE SCHEMA IF NOT EXISTS my_project.analytics;

-- Create table
CREATE TABLE my_project.analytics.customers (
  customer_id INT64,
  name STRING,
  email STRING,
  created_at TIMESTAMP,
  country STRING
);

-- Insert data
INSERT INTO my_project.analytics.customers VALUES
  (1, 'Alice', 'alice@example.com', CURRENT_TIMESTAMP(), 'VN'),
  (2, 'Bob', 'bob@example.com', CURRENT_TIMESTAMP(), 'US'),
  (3, 'Charlie', 'charlie@example.com', CURRENT_TIMESTAMP(), 'VN');

-- Basic queries
SELECT
  country,
  COUNT(*) as customer_count
FROM my_project.analytics.customers
GROUP BY country
ORDER BY customer_count DESC;
```

### Public Datasets để explore

```sql
-- GitHub activity
SELECT
  repo_name,
  COUNT(*) as commits
FROM `bigquery-public-data.github_repos.commits`
WHERE committer.date > '2024-01-01'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 20;

-- Stack Overflow
SELECT
  tags,
  COUNT(*) as question_count
FROM `bigquery-public-data.stackoverflow.posts_questions`
WHERE creation_date >= '2024-01-01'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 20;
```

### Bài tập

1. **Sandbox setup** — Tạo BigQuery sandbox, run first query trên public dataset
2. **Create dataset + table** — Tạo dataset, table, insert data, query
3. **Public datasets** — Explore 3 public datasets (GitHub, StackOverflow, USA names)
4. **Cost estimation** — Dùng dry-run để xem query sẽ scan bao nhiêu data

### Resources

- 📖 [BigQuery Docs](https://cloud.google.com/bigquery/docs)
- 📖 [BigQuery Sandbox](https://cloud.google.com/bigquery/docs/sandbox)
- 📖 [Public Datasets](https://cloud.google.com/bigquery/public-data)
- 🎬 [Google Cloud Skills Boost — BigQuery](https://www.cloudskillsboost.google/paths/12)


---

## Level 2: Intermediate (2–4 tuần)

### Partitioned Tables

```sql
-- Date-partitioned table
CREATE TABLE my_project.analytics.events
PARTITION BY DATE(event_timestamp)
CLUSTER BY user_id, event_type
AS
SELECT
  GENERATE_UUID() as event_id,
  CAST(FLOOR(RAND() * 1000) AS INT64) as user_id,
  CASE CAST(FLOOR(RAND() * 3) AS INT64)
    WHEN 0 THEN 'page_view'
    WHEN 1 THEN 'click'
    ELSE 'purchase'
  END as event_type,
  TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL CAST(FLOOR(RAND() * 30) AS INT64) DAY) as event_timestamp
FROM UNNEST(GENERATE_ARRAY(1, 10000));

-- Query with partition filter (giảm cost)
SELECT
  event_type,
  COUNT(*) as cnt
FROM my_project.analytics.events
WHERE DATE(event_timestamp) BETWEEN '2024-01-01' AND '2024-01-31'
GROUP BY 1;
```

### STRUCT & ARRAY

```sql
-- STRUCT (nested records)
SELECT
  'Alice' as name,
  STRUCT(
    '123 Main St' as street,
    'Hanoi' as city,
    'VN' as country
  ) as address,
  ['python', 'sql', 'dbt'] as skills;

-- Query nested data
SELECT
  name,
  address.city,
  skill
FROM my_project.analytics.users,
UNNEST(skills) as skill
WHERE 'python' IN UNNEST(skills);

-- ARRAY_AGG
SELECT
  department,
  ARRAY_AGG(STRUCT(name, salary) ORDER BY salary DESC LIMIT 5) as top_earners
FROM my_project.analytics.employees
GROUP BY department;
```

### Scheduled Queries

```sql
-- Via Console: BigQuery > Scheduled Queries > Create
-- Or via bq CLI:
-- bq mk --transfer_config \
--   --target_dataset=analytics \
--   --display_name='Daily Summary' \
--   --schedule='every 24 hours' \
--   --params='{"query":"SELECT ..."}'

-- Example scheduled query
CREATE OR REPLACE TABLE my_project.analytics.daily_summary
PARTITION BY summary_date
AS
SELECT
  CURRENT_DATE() as summary_date,
  COUNT(DISTINCT user_id) as dau,
  COUNT(*) as total_events,
  COUNTIF(event_type = 'purchase') as purchases
FROM my_project.analytics.events
WHERE DATE(event_timestamp) = CURRENT_DATE() - 1;
```

### Materialized Views

```sql
-- Auto-refreshed materialized view
CREATE MATERIALIZED VIEW my_project.analytics.mv_daily_revenue
PARTITION BY order_date
CLUSTER BY product_category
AS
SELECT
  DATE(order_timestamp) as order_date,
  product_category,
  SUM(amount) as total_revenue,
  COUNT(*) as order_count
FROM my_project.analytics.orders
GROUP BY 1, 2;

-- Query like normal table (auto-uses materialized data)
SELECT * FROM my_project.analytics.mv_daily_revenue
WHERE order_date = '2024-06-01';
```

### UDFs (User-Defined Functions)

```sql
-- SQL UDF
CREATE FUNCTION my_project.analytics.mask_email(email STRING)
RETURNS STRING
AS (
  CONCAT(
    LEFT(email, 2),
    '***@',
    SPLIT(email, '@')[OFFSET(1)]
  )
);

-- JavaScript UDF
CREATE FUNCTION my_project.analytics.parse_user_agent(ua STRING)
RETURNS STRUCT<browser STRING, os STRING>
LANGUAGE js AS r"""
  var result = {browser: 'unknown', os: 'unknown'};
  if (ua.includes('Chrome')) result.browser = 'Chrome';
  else if (ua.includes('Firefox')) result.browser = 'Firefox';
  if (ua.includes('Windows')) result.os = 'Windows';
  else if (ua.includes('Mac')) result.os = 'macOS';
  return result;
""";

-- Usage
SELECT
  my_project.analytics.mask_email(email) as masked_email,
  my_project.analytics.parse_user_agent(user_agent) as ua_info
FROM my_project.analytics.users;
```

### Bài tập

1. **Partitioned table** — Tạo table partition by date, so sánh cost có/không partition filter
2. **STRUCT + ARRAY** — Model nested data (user → addresses, orders → items)
3. **Materialized view** — Tạo MV cho aggregation thường dùng, measure performance gain
4. **UDF** — Viết 2 UDFs: SQL UDF (mask PII) + JS UDF (parse complex string)

### Resources

- 📖 [Partitioning Guide](https://cloud.google.com/bigquery/docs/partitioned-tables)
- 📖 [Working with Arrays/Structs](https://cloud.google.com/bigquery/docs/reference/standard-sql/arrays)
- 📖 [Materialized Views](https://cloud.google.com/bigquery/docs/materialized-views-intro)
- 📖 [UDFs](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions)

---

## Level 3: Advanced (4–8 tuần)

### BigQuery ML

```sql
-- Train a model directly in SQL
CREATE OR REPLACE MODEL my_project.analytics.churn_model
OPTIONS(
  model_type='LOGISTIC_REG',
  input_label_cols=['churned'],
  auto_class_weights=TRUE
) AS
SELECT
  days_since_last_login,
  total_purchases,
  avg_session_duration,
  support_tickets,
  churned
FROM my_project.analytics.customer_features
WHERE signup_date < '2024-01-01';

-- Predict
SELECT
  customer_id,
  predicted_churned,
  predicted_churned_probs
FROM ML.PREDICT(
  MODEL my_project.analytics.churn_model,
  (SELECT * FROM my_project.analytics.customer_features
   WHERE signup_date >= '2024-01-01')
);

-- Evaluate
SELECT * FROM ML.EVALUATE(
  MODEL my_project.analytics.churn_model,
  (SELECT * FROM my_project.analytics.customer_features_test)
);
```

### BI Engine & Performance

```sql
-- BI Engine: in-memory acceleration for dashboards
-- Configure via Console: BigQuery > BI Engine > Create Reservation

-- Optimize queries with INFORMATION_SCHEMA
SELECT
  table_name,
  total_rows,
  total_logical_bytes / POW(2,30) as size_gb,
  TIMESTAMP_DIFF(CURRENT_TIMESTAMP(), last_modified_time, HOUR) as hours_since_update
FROM my_project.analytics.INFORMATION_SCHEMA.TABLE_STORAGE
ORDER BY total_logical_bytes DESC;

-- Query performance analysis
SELECT
  job_id,
  total_bytes_processed / POW(2,30) as gb_processed,
  total_slot_ms / 1000 as slot_seconds,
  query
FROM `region-us`.INFORMATION_SCHEMA.JOBS_BY_PROJECT
WHERE creation_time > TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 1 DAY)
ORDER BY total_bytes_processed DESC
LIMIT 20;
```

### Remote Functions (Cloud Functions integration)

```sql
-- Create connection to Cloud Functions
CREATE FUNCTION my_project.analytics.sentiment_analysis(text STRING)
RETURNS FLOAT64
REMOTE WITH CONNECTION `my_project.us.my_connection`
OPTIONS (
  endpoint = 'https://us-central1-my-project.cloudfunctions.net/sentiment'
);

-- Use in queries
SELECT
  review_text,
  my_project.analytics.sentiment_analysis(review_text) as sentiment_score
FROM my_project.analytics.product_reviews
WHERE review_date = CURRENT_DATE() - 1;
```

### BigQuery Omni (Multi-cloud)

```sql
-- Query data in AWS S3 (via BigQuery Omni)
CREATE EXTERNAL TABLE my_project.analytics.s3_sales
WITH CONNECTION `aws-us-east-1.my-aws-connection`
OPTIONS (
  format = 'PARQUET',
  uris = ['s3://my-bucket/sales/*.parquet']
);

SELECT
  region,
  SUM(revenue) as total_revenue
FROM my_project.analytics.s3_sales
WHERE sale_date >= '2024-01-01'
GROUP BY region;
```

### Bài tập

1. **BigQuery ML** — Train classification model, evaluate, predict trên real data
2. **Performance audit** — Dùng INFORMATION_SCHEMA analyze top expensive queries, optimize
3. **Remote function** — Kết nối Cloud Function (sentiment/translation) vào BigQuery
4. **Cost optimization** — Implement slot reservations strategy cho team

### Resources

- 📖 [BigQuery ML Docs](https://cloud.google.com/bigquery/docs/bqml-introduction)
- 📖 [BI Engine](https://cloud.google.com/bigquery/docs/bi-engine-intro)
- 📖 [BigQuery Omni](https://cloud.google.com/bigquery/docs/omni-introduction)
- 📖 [Remote Functions](https://cloud.google.com/bigquery/docs/remote-functions)
- 📖 [Cost Optimization](https://cloud.google.com/bigquery/docs/best-practices-costs)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **BigQuery** | Serverless DW | Zero-admin, scale vô hạn, ML built-in | GCP lock-in, cost unpredictable |
| **Redshift** | Managed DW | AWS native, Spectrum, RA3 nodes | Cluster management, resize pain |
| **Snowflake** | Multi-cloud DW | Separation compute/storage, sharing | Cost at scale, credits model |
| **Databricks SQL** | Lakehouse | Unity Catalog, Spark integration | More complex, Spark overhead |
| **ClickHouse** | OLAP DB | Blazing fast, open-source, real-time | Self-managed, less ecosystem |

### Khi nào chọn gì?

- **BigQuery** → GCP ecosystem, serverless preference, ad-hoc analytics, BigQuery ML
- **Redshift** → AWS ecosystem, steady workloads, cần Spectrum cho S3 data
- **Snowflake** → Multi-cloud, data sharing, predictable pricing, diverse workloads
- **Databricks SQL** → Lakehouse architecture, heavy ML workloads, Delta Lake
- **ClickHouse** → Real-time analytics, high-concurrency reads, cost-sensitive
