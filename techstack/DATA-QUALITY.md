# ✅ Data Quality — Learning Guide (Beginner → Advanced)

## Tổng quan

**Data Quality** là tập hợp tools & frameworks đảm bảo data đáp ứng tiêu chuẩn chất lượng trước khi sử dụng. Bao gồm: dbt tests, Great Expectations, Soda, Elementary, và data contracts.

**6 Dimensions of Data Quality:**
- **Accuracy** — Data phản ánh đúng thực tế
- **Completeness** — Không thiếu data quan trọng
- **Freshness** — Data được update đúng thời hạn
- **Consistency** — Data nhất quán giữa các hệ thống
- **Uniqueness** — Không có duplicates
- **Validity** — Data đúng format, range, business rules

**Ai dùng:** Data Engineers, Analytics Engineers, Data Governance teams

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Data Test** | Assertion kiểm tra data quality |
| **Expectation** | Rule mô tả data "should" look like |
| **Check** | Declarative validation statement |
| **Threshold** | Acceptable failure rate |
| **Severity** | warn vs error (block pipeline or not) |
| **Freshness** | How recent data should be |
| **Contract** | Agreement between producer and consumer |

### dbt Built-in Tests

```yaml
# models/staging/_schema.yml
version: 2
models:
  - name: stg_orders
    description: "Staging orders table"
    columns:
      - name: order_id
        description: "Unique order identifier"
        tests:
          - unique
          - not_null
      
      - name: customer_id
        description: "FK to customers"
        tests:
          - not_null
          - relationships:
              to: ref('stg_customers')
              field: customer_id
      
      - name: status
        description: "Order status"
        tests:
          - accepted_values:
              values: ['pending', 'processing', 'shipped', 'delivered', 'cancelled']
      
      - name: amount
        description: "Order total"
        tests:
          - not_null
          - dbt_utils.expression_is_true:
              expression: ">= 0"
      
      - name: created_at
        tests:
          - not_null
          - dbt_utils.expression_is_true:
              expression: "<= current_timestamp"
```

```bash
# Run tests
dbt test                              # all tests
dbt test -s stg_orders                # specific model
dbt test --select tag:critical        # tagged tests only
dbt build                             # run + test together
```

### Custom dbt Tests

```sql
-- tests/generic/test_positive_value.sql
{% test positive_value(model, column_name) %}
SELECT {{ column_name }}
FROM {{ model }}
WHERE {{ column_name }} < 0
{% endtest %}

-- tests/generic/test_row_count_within_range.sql
{% test row_count_within_range(model, min_count, max_count) %}
SELECT COUNT(*) AS row_count
FROM {{ model }}
HAVING COUNT(*) < {{ min_count }} OR COUNT(*) > {{ max_count }}
{% endtest %}
```

```yaml
# Usage in schema.yml
columns:
  - name: revenue
    tests:
      - positive_value
  
models:
  - name: daily_sales
    tests:
      - row_count_within_range:
          min_count: 100
          max_count: 100000
```

### Understanding Quality Dimensions

```yaml
# Map tests to quality dimensions:

# UNIQUENESS
- unique  # no duplicates
- dbt_utils.unique_combination_of_columns:
    combination_of_columns: [date, product_id]

# COMPLETENESS
- not_null
- dbt_utils.not_null_proportion:
    at_least: 0.95  # allow 5% nulls

# VALIDITY
- accepted_values:
    values: ['active', 'inactive']
- dbt_utils.expression_is_true:
    expression: "length(phone) >= 10"

# CONSISTENCY
- relationships:
    to: ref('dim_products')
    field: product_id

# FRESHNESS (source freshness)
sources:
  - name: raw
    freshness:
      warn_after: {count: 12, period: hour}
      error_after: {count: 24, period: hour}
    loaded_at_field: _loaded_at
```

### Bài tập

1. **dbt tests** — Thêm 10+ tests cho existing models (unique, not_null, accepted_values)
2. **Custom test** — Viết 2 generic tests (positive_value, valid_email)
3. **Source freshness** — Configure freshness checks cho 3 sources
4. **Quality dimensions** — Map mỗi test vào quality dimension tương ứng

### Resources

- 📖 [dbt Tests Docs](https://docs.getdbt.com/docs/build/data-tests)
- 📖 [dbt-utils Tests](https://github.com/dbt-labs/dbt-utils#generic-tests)
- 📖 [Source Freshness](https://docs.getdbt.com/docs/build/sources#source-data-freshness)
- 📖 [Data Quality Fundamentals (O'Reilly)](https://www.oreilly.com/library/view/data-quality-fundamentals/9781098112035/)


---

## Level 2: Intermediate (2–4 tuần)

### Great Expectations

```bash
# Install
pip install great_expectations

# Init project
great_expectations init
# Creates: great_expectations/ directory with config
```

```python
# Create expectation suite programmatically
import great_expectations as gx

context = gx.get_context()

# Connect to data source
datasource = context.sources.add_pandas("my_pandas_ds")
data_asset = datasource.add_dataframe_asset(name="orders_df")

# Create batch request
batch_request = data_asset.build_batch_request(dataframe=orders_df)

# Create expectations
validator = context.get_validator(
    batch_request=batch_request,
    expectation_suite_name="orders_suite"
)

# Add expectations
validator.expect_column_values_to_not_be_null("order_id")
validator.expect_column_values_to_be_unique("order_id")
validator.expect_column_values_to_be_between("amount", min_value=0, max_value=1000000)
validator.expect_column_values_to_be_in_set(
    "status", ["pending", "completed", "cancelled"]
)
validator.expect_column_pair_values_a_to_be_greater_than_b(
    "updated_at", "created_at"
)
validator.expect_table_row_count_to_be_between(min_value=1000, max_value=500000)

# Save suite
validator.save_expectation_suite(discard_failed_expectations=False)
```

```python
# Run checkpoint (validation)
checkpoint = context.add_or_update_checkpoint(
    name="orders_checkpoint",
    validations=[{
        "batch_request": batch_request,
        "expectation_suite_name": "orders_suite",
    }],
    action_list=[
        {"name": "store_validation_result", "action": {"class_name": "StoreValidationResultAction"}},
        {"name": "update_data_docs", "action": {"class_name": "UpdateDataDocsAction"}},
    ]
)

result = checkpoint.run()
print(f"Success: {result.success}")
# Open Data Docs: great_expectations/uncommitted/data_docs/local_site/index.html
```

### Soda

```bash
# Install
pip install soda-core-postgres  # or soda-core-bigquery, soda-core-redshift
```

```yaml
# configuration.yml
data_source my_warehouse:
  type: postgres
  host: localhost
  port: 5432
  username: analyst
  password: ${POSTGRES_PASSWORD}
  database: analytics
  schema: public
```

```yaml
# checks/orders_checks.yml
checks for orders:
  # Row count
  - row_count > 0
  - row_count between 1000 and 500000
  
  # Freshness
  - freshness(created_at) < 24h
  
  # Uniqueness
  - duplicate_count(order_id) = 0
  
  # Completeness
  - missing_count(customer_id) = 0
  - missing_percent(email) < 5%
  
  # Validity
  - invalid_count(status) = 0:
      valid values: [pending, completed, cancelled]
  - invalid_percent(amount) = 0%:
      valid min: 0
  
  # Custom SQL
  - failed rows:
      name: Orders with future dates
      fail query: |
        SELECT * FROM orders
        WHERE created_at > CURRENT_TIMESTAMP
  
  # Schema check
  - schema:
      fail:
        when required column missing: [order_id, customer_id, amount]
        when wrong type:
          order_id: integer
          amount: numeric
```

```bash
# Run checks
soda scan -d my_warehouse -c configuration.yml checks/orders_checks.yml

# Output:
# Scan summary:
# 8/8 checks PASSED
# All is good. No failures. No warnings. No errors.
```

### dbt-expectations Package

```yaml
# packages.yml
packages:
  - package: calogica/dbt_expectations
    version: [">=0.10.0", "<0.11.0"]
```

```yaml
# schema.yml — Advanced tests from dbt_expectations
models:
  - name: fact_orders
    tests:
      # Table-level
      - dbt_expectations.expect_table_row_count_to_be_between:
          min_value: 1000
          max_value: 1000000
      - dbt_expectations.expect_table_row_count_to_equal_other_table:
          compare_model: ref("stg_orders")
    columns:
      - name: order_date
        tests:
          - dbt_expectations.expect_column_values_to_be_between:
              min_value: "'2020-01-01'"
              max_value: "current_date"
              row_condition: "status != 'cancelled'"
          - dbt_expectations.expect_row_values_to_have_recent_data:
              datepart: day
              interval: 1
      
      - name: email
        tests:
          - dbt_expectations.expect_column_values_to_match_regex:
              regex: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$"
      
      - name: amount
        tests:
          - dbt_expectations.expect_column_mean_to_be_between:
              min_value: 50
              max_value: 500
          - dbt_expectations.expect_column_stdev_to_be_between:
              min_value: 0
              max_value: 200
      
      - name: quantity
        tests:
          - dbt_expectations.expect_column_quantile_values_to_be_between:
              quantile: 0.95
              min_value: 1
              max_value: 100
```

### Bài tập

1. **Great Expectations** — Create suite cho orders table, run checkpoint, view Data Docs
2. **Soda** — Write 10 checks, integrate vào CI/CD pipeline
3. **dbt_expectations** — Add 15+ advanced tests (regex, statistical, freshness)
4. **Comparison** — Test same assertions in GX vs Soda vs dbt, compare DX

### Resources

- 📖 [Great Expectations Docs](https://docs.greatexpectations.io/)
- 📖 [Soda Docs](https://docs.soda.io/)
- 📖 [dbt_expectations](https://github.com/calogica/dbt-expectations)
- 📖 [Soda Checks Reference](https://docs.soda.io/soda-cl/soda-cl-overview.html)

---

## Level 3: Advanced (4–8 tuần)

### Elementary (dbt-native Observability)

```yaml
# packages.yml
packages:
  - package: elementary-data/elementary
    version: [">=0.15.0", "<0.16.0"]
```

```yaml
# models/schema.yml — Elementary anomaly detection
models:
  - name: fact_daily_revenue
    tests:
      # Anomaly detection — auto-learns patterns
      - elementary.volume_anomalies:
          timestamp_column: date
          where_expression: "date >= dateadd('day', -60, current_date)"
          severity: warn
      
      - elementary.freshness_anomalies:
          timestamp_column: updated_at
          severity: error
    
    columns:
      - name: revenue
        tests:
          - elementary.column_anomalies:
              column_anomalies:
                - sum
                - avg
                - zero_count
              timestamp_column: date
              severity: warn
      
      - name: order_count
        tests:
          - elementary.column_anomalies:
              column_anomalies:
                - count
                - null_count
              timestamp_column: date
```

```bash
# Install Elementary CLI
pip install elementary-data[bigquery]  # or [redshift], [snowflake]

# Generate report
edr report --profile-target prod

# Send Slack alerts
edr monitor --slack-webhook $SLACK_WEBHOOK

# Send to Elementary Cloud
edr send-report --elementary-cloud-api-key $API_KEY
```

### Data Contracts

```yaml
# data_contract.yml — Agreement between producer & consumer
apiVersion: v1
kind: DataContract
metadata:
  name: orders-contract
  version: "2.0.0"
  owner: data-engineering-team
  
schema:
  type: object
  properties:
    order_id:
      type: integer
      description: "Unique order identifier"
      constraints:
        - unique
        - not_null
    customer_id:
      type: integer
      constraints: [not_null]
    amount:
      type: decimal
      constraints:
        - not_null
        - minimum: 0
    status:
      type: string
      enum: [pending, processing, shipped, delivered, cancelled]
    created_at:
      type: timestamp
      constraints: [not_null]

quality:
  freshness:
    max_delay: 4h
  completeness:
    min_percentage: 99.5
  volume:
    min_rows_per_day: 1000
    max_rows_per_day: 100000

sla:
  availability: 99.9%
  response_time: 5s
  
consumers:
  - team: analytics
    use_case: "Daily revenue reporting"
  - team: marketing
    use_case: "Customer segmentation"

breaking_changes:
  notification: [email, slack]
  approval_required: true
```

### Custom Quality Framework

```python
# quality_framework.py — Custom DQ framework
from dataclasses import dataclass
from enum import Enum
from datetime import datetime, timedelta
import pandas as pd

class Severity(Enum):
    INFO = "info"
    WARNING = "warning"
    CRITICAL = "critical"

class Dimension(Enum):
    ACCURACY = "accuracy"
    COMPLETENESS = "completeness"
    FRESHNESS = "freshness"
    CONSISTENCY = "consistency"
    UNIQUENESS = "uniqueness"
    VALIDITY = "validity"

@dataclass
class QualityCheck:
    name: str
    dimension: Dimension
    severity: Severity
    query: str
    threshold: float = 0.0  # max acceptable failure rate
    
@dataclass
class CheckResult:
    check: QualityCheck
    passed: bool
    actual_value: float
    details: str
    executed_at: datetime

class DataQualityRunner:
    def __init__(self, connection):
        self.connection = connection
        self.results: list[CheckResult] = []
    
    def run_check(self, check: QualityCheck) -> CheckResult:
        """Execute a single quality check."""
        result = pd.read_sql(check.query, self.connection)
        failure_count = result.iloc[0, 0] if len(result) > 0 else 0
        
        total_query = check.query.replace("SELECT COUNT", "SELECT COUNT(*) FROM")
        total = pd.read_sql(f"SELECT COUNT(*) FROM ({check.query.split('FROM')[1].split('WHERE')[0]})", self.connection)
        
        failure_rate = failure_count / max(total.iloc[0, 0], 1)
        passed = failure_rate <= check.threshold
        
        result = CheckResult(
            check=check,
            passed=passed,
            actual_value=failure_rate,
            details=f"{failure_count} failures ({failure_rate:.2%})",
            executed_at=datetime.utcnow()
        )
        self.results.append(result)
        return result
    
    def run_suite(self, checks: list[QualityCheck]) -> dict:
        """Run all checks and return summary."""
        for check in checks:
            self.run_check(check)
        
        return {
            "total": len(self.results),
            "passed": sum(1 for r in self.results if r.passed),
            "failed": sum(1 for r in self.results if not r.passed),
            "critical_failures": [
                r for r in self.results 
                if not r.passed and r.check.severity == Severity.CRITICAL
            ]
        }

# Usage
checks = [
    QualityCheck(
        name="orders_no_duplicates",
        dimension=Dimension.UNIQUENESS,
        severity=Severity.CRITICAL,
        query="SELECT COUNT(*) - COUNT(DISTINCT order_id) FROM orders",
        threshold=0.0
    ),
    QualityCheck(
        name="orders_completeness",
        dimension=Dimension.COMPLETENESS,
        severity=Severity.WARNING,
        query="SELECT COUNT(*) FROM orders WHERE customer_id IS NULL",
        threshold=0.01  # allow 1% nulls
    ),
]
```

### SLA Monitoring

```python
# sla_monitor.py — Track data SLAs
from datetime import datetime, timedelta
import requests

class SLAMonitor:
    def __init__(self, warehouse_conn, slack_webhook: str):
        self.conn = warehouse_conn
        self.slack_webhook = slack_webhook
    
    def check_freshness_sla(self, table: str, max_delay_hours: int) -> bool:
        """Check if table meets freshness SLA."""
        query = f"""
            SELECT MAX(updated_at) as last_update
            FROM {table}
        """
        result = pd.read_sql(query, self.conn)
        last_update = result["last_update"].iloc[0]
        
        delay = datetime.utcnow() - last_update
        sla_met = delay < timedelta(hours=max_delay_hours)
        
        if not sla_met:
            self.alert(
                f"🚨 SLA BREACH: {table}\n"
                f"Last update: {last_update}\n"
                f"Delay: {delay}\n"
                f"SLA: {max_delay_hours}h"
            )
        return sla_met
    
    def alert(self, message: str):
        requests.post(self.slack_webhook, json={"text": message})
```

### Bài tập

1. **Elementary** — Setup anomaly detection, generate report, review alerts
2. **Data contract** — Write contract cho 2 critical tables, implement validation
3. **Custom framework** — Build DQ framework chạy nightly, store results, send alerts
4. **SLA dashboard** — Monitor freshness + completeness SLAs, visualize trends

### Resources

- 📖 [Elementary Docs](https://docs.elementary-data.com/)
- 📖 [Data Contracts — Andrew Jones](https://datacontract.com/)
- 📖 [dbt Data Tests Best Practices](https://docs.getdbt.com/blog/data-tests-best-practices)
- 💻 [Elementary GitHub](https://github.com/elementary-data/elementary)
- 📖 [Data Quality at Scale (Uber)](https://www.uber.com/blog/data-quality-at-uber/)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **dbt tests** | Built-in | Free, integrated, SQL-based | Basic stats, no anomaly detection |
| **dbt_expectations** | dbt package | Statistical tests, GX-inspired | Still dbt-only |
| **Great Expectations** | Python framework | Comprehensive, Data Docs UI | Complex setup, verbose |
| **Soda** | YAML-based | Declarative, easy to read, Soda Cloud | Paid features, newer |
| **Elementary** | dbt-native | Anomaly detection, lineage-aware | dbt-only, newer |
| **Monte Carlo** | SaaS | ML-powered, full observability | Expensive, enterprise-only |

### Khi nào chọn gì?

- **dbt tests** → Đã dùng dbt, basic quality checks đủ
- **dbt_expectations** → Cần statistical tests nhưng muốn stay in dbt
- **Great Expectations** → Python team, comprehensive validation, standalone
- **Soda** → Declarative preference, multi-tool pipeline, easy YAML
- **Elementary** → dbt team cần anomaly detection + observability
- **Monte Carlo** → Enterprise cần ML-powered auto-detection, budget ok
