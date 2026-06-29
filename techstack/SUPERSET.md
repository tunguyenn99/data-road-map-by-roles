# 🦸 Apache Superset — Learning Guide (Beginner → Advanced)

## Tổng quan

**Apache Superset** là open-source modern BI platform, hỗ trợ explore data qua SQL Lab, tạo charts & dashboards interactive. Superset kết nối hầu hết databases qua SQLAlchemy và có thể deploy on-premise hoặc cloud.

**Use cases:**
- Self-service BI dashboards
- SQL exploration (SQL Lab)
- Embedded analytics trong products
- Data democratization cho toàn tổ chức

**Ai dùng:** Data Analysts, Analytics Engineers, BI Developers, Data-savvy business users

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Database** | Connection tới DB (PostgreSQL, MySQL, BigQuery...) |
| **Dataset** | Table/view registered trong Superset để visualize |
| **Chart** | Visualization từ dataset (30+ chart types) |
| **Dashboard** | Collection of charts, filters, layout |
| **SQL Lab** | Interactive SQL editor với autocomplete |
| **Saved Query** | SQL query lưu lại để reuse |

### Docker Install

```bash
# Clone Superset repo
git clone https://github.com/apache/superset.git
cd superset

# Start with Docker Compose
docker compose -f docker-compose-non-dev.yml up -d

# Access at http://localhost:8088
# Default credentials: admin / admin
```

```bash
# Alternative: pip install (for development)
pip install apache-superset

# Initialize
superset db upgrade
superset fab create-admin \
  --username admin \
  --firstname Admin \
  --lastname User \
  --email admin@example.com \
  --password admin

superset init
superset run -p 8088 --with-threads --reload
```

### Connect Database

```python
# PostgreSQL connection string
postgresql+psycopg2://user:password@host:5432/database

# MySQL
mysql+pymysql://user:password@host:3306/database

# BigQuery
bigquery://project_id

# Redshift
redshift+psycopg2://user:password@host:5439/database

# DuckDB (local analytics)
duckdb:///path/to/database.db
```

### First Chart + Dashboard

```
1. Settings > Database Connections > + Database
2. Add connection string, test connection
3. Datasets > + Dataset > chọn table
4. Charts > + Chart > chọn dataset
5. Configure:
   - Chart type: Bar Chart
   - Dimension: category
   - Metric: SUM(revenue)
   - Filters: date >= 2024-01-01
6. Save to Dashboard
7. Dashboard > arrange layout > save
```

### Bài tập

1. **Docker setup** — Cài Superset via Docker, login, explore examples
2. **Connect DB** — Kết nối PostgreSQL/MySQL, register 2 datasets
3. **Create charts** — Tạo 5 chart types (bar, line, pie, table, big number)
4. **Dashboard** — Gộp charts vào dashboard, thêm filter box

### Resources

- 📖 [Superset Docs](https://superset.apache.org/docs/intro)
- 📖 [Installation Guide](https://superset.apache.org/docs/installation/docker-compose)
- 💻 [Superset GitHub](https://github.com/apache/superset)
- 🎬 [Superset Tutorial — Preset](https://preset.io/blog/apache-superset-tutorial/)


---

## Level 2: Intermediate (2–4 tuần)

### Custom SQL Metrics

```sql
-- Trong Dataset configuration, thêm custom metrics:

-- Metric: revenue_per_user
SELECT SUM(revenue) / COUNT(DISTINCT user_id)

-- Metric: conversion_rate
SELECT 
  CAST(COUNT(CASE WHEN event = 'purchase' THEN 1 END) AS FLOAT) 
  / NULLIF(COUNT(CASE WHEN event = 'page_view' THEN 1 END), 0)

-- Metric: month_over_month_growth
SELECT 
  (SUM(CASE WHEN date >= DATE_TRUNC('month', CURRENT_DATE) THEN revenue END) -
   SUM(CASE WHEN date >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '1 month') 
            AND date < DATE_TRUNC('month', CURRENT_DATE) THEN revenue END))
  / NULLIF(SUM(CASE WHEN date >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '1 month') 
            AND date < DATE_TRUNC('month', CURRENT_DATE) THEN revenue END), 0)
```

### Jinja Templates in SQL Lab

```sql
-- Date filter with Jinja
SELECT *
FROM orders
WHERE order_date >= '{{ from_dttm }}'
  AND order_date < '{{ to_dttm }}'

-- User-defined parameters
{% set schema = "production" if filter_values('env')[0] == 'prod' else "staging" %}
SELECT * FROM {{ schema }}.customers

-- Conditional logic
SELECT
  customer_id,
  {% if columns == "full" %}
    name, email, phone, address,
  {% else %}
    name,
  {% endif %}
  total_orders
FROM customers

-- Macro for repeated patterns
{% macro date_filter(column, granularity='day') %}
  {{ column }} >= DATE_TRUNC('{{ granularity }}', '{{ from_dttm }}'::DATE)
  AND {{ column }} < DATE_TRUNC('{{ granularity }}', '{{ to_dttm }}'::DATE) + INTERVAL '1 {{ granularity }}'
{% endmacro %}

SELECT department, COUNT(*) as headcount
FROM employees
WHERE {{ date_filter('hire_date', 'month') }}
GROUP BY 1
```

### Cross-Filters & Dashboard Interactions

```
Cross-filter configuration:
1. Dashboard > Edit > Settings
2. Enable "Cross-filtering"
3. Charts trong cùng dashboard tự động filter lẫn nhau

Ví dụ:
- Click "Electronics" trong Bar Chart
- → Line Chart tự filter chỉ hiện Electronics
- → Table chỉ hiện rows of Electronics
```

### Alerts & Reports

```
Settings > Alerts & Reports:

Alert:
  - Name: "Revenue Drop Alert"
  - Database: production_db
  - SQL: SELECT SUM(revenue) FROM daily_sales WHERE date = CURRENT_DATE - 1
  - Condition: value < 50000
  - Action: Send Slack/Email notification

Report:
  - Name: "Weekly Dashboard PDF"
  - Dashboard: Executive Summary
  - Schedule: Every Monday 8:00 AM
  - Recipients: team@company.com
  - Format: PDF attachment
```

### Bài tập

1. **Custom metrics** — Tạo 5 custom SQL metrics trên dataset
2. **Jinja SQL** — Viết parameterized queries với Jinja trong SQL Lab
3. **Cross-filters** — Dashboard 5+ charts với cross-filtering hoạt động
4. **Alerts** — Setup alert khi metric vượt threshold

### Resources

- 📖 [SQL Templating (Jinja)](https://superset.apache.org/docs/configuration/sql-templating)
- 📖 [Dashboard Filters](https://superset.apache.org/docs/using-superset/exploring-data)
- 📖 [Alerts & Reports](https://superset.apache.org/docs/configuration/alerts-reports)
- 📖 [Creating Charts](https://superset.apache.org/docs/using-superset/creating-your-first-dashboard)

---

## Level 3: Advanced (4–8 tuần)

### Custom Visualization Plugin (React)

```bash
# Scaffold custom viz plugin
npx @superset-ui/generator-superset create-plugin \
  --name my-custom-chart \
  --type chart

cd plugins/my-custom-chart
```

```typescript
// src/MyCustomChart.tsx
import React from 'react';
import { SupersetPluginChartProps } from './types';

export default function MyCustomChart(props: SupersetPluginChartProps) {
  const { data, height, width, colorScheme } = props;
  
  return (
    <div style={{ width, height, overflow: 'auto' }}>
      <svg width={width} height={height}>
        {data.map((d, i) => (
          <rect
            key={i}
            x={i * (width / data.length)}
            y={height - d.value * (height / Math.max(...data.map(x => x.value)))}
            width={(width / data.length) - 2}
            height={d.value * (height / Math.max(...data.map(x => x.value)))}
            fill={colorScheme[i % colorScheme.length]}
          />
        ))}
      </svg>
    </div>
  );
}
```

```python
# Register plugin in superset_config.py
EXTRA_CHART_PLUGINS = [
    {
        "key": "my-custom-chart",
        "package": "@my-org/superset-plugin-chart-custom",
    }
]
```

### Embedded Analytics

```python
# superset_config.py — Enable embedding
FEATURE_FLAGS = {
    "EMBEDDED_SUPERSET": True,
    "EMBEDDABLE_CHARTS": True,
}

# CORS for embedding domain
CORS_OPTIONS = {
    "supports_credentials": True,
    "allow_headers": ["*"],
    "resources": ["/api/*"],
    "origins": ["https://your-app.com"],
}

# Guest token API (for secure embedding)
GUEST_ROLE_NAME = "Gamma"
GUEST_TOKEN_JWT_SECRET = "your-secret-key"
GUEST_TOKEN_JWT_EXP = 300  # 5 minutes
```

```javascript
// Frontend embedding with Superset SDK
import { embedDashboard } from "@superset-ui/embedded-sdk";

async function renderDashboard() {
  const guestToken = await fetchGuestToken(); // from your backend
  
  embedDashboard({
    id: "dashboard-uuid",
    supersetDomain: "https://superset.your-company.com",
    mountPoint: document.getElementById("superset-container"),
    fetchGuestToken: () => guestToken,
    dashboardUiConfig: {
      hideTitle: true,
      hideChartControls: true,
      filters: {
        visible: true,
        expanded: false,
      },
    },
  });
}
```

### RBAC (Role-Based Access Control)

```python
# superset_config.py
AUTH_TYPE = AUTH_OAUTH  # or AUTH_LDAP, AUTH_DB

# Row-Level Security
# Configure via UI: Settings > Row Level Security

# Dataset-level permissions
# Roles > Create Role > Add permissions:
#   - datasource access on [database].[schema].[table]
#   - schema access on [database].[schema]

# Custom security manager
from superset.security import SupersetSecurityManager

class CustomSecurityManager(SupersetSecurityManager):
    def can_access_chart(self, chart):
        # Custom logic
        user_department = g.user.department
        return chart.dataset.schema == f"dept_{user_department}"

CUSTOM_SECURITY_MANAGER = CustomSecurityManager
```

### Helm Deployment (Kubernetes)

```yaml
# values.yaml for Helm chart
replicaCount: 2

image:
  repository: apache/superset
  tag: "3.1.0"

supersetNode:
  resources:
    requests:
      cpu: "1000m"
      memory: "2Gi"
    limits:
      cpu: "2000m"
      memory: "4Gi"

postgresql:
  enabled: true
  auth:
    password: "superset-metadata-pass"

redis:
  enabled: true

configOverrides:
  secret: |
    SECRET_KEY = 'your-super-secret-key'
    SQLALCHEMY_DATABASE_URI = 'postgresql://...'
    
  celery: |
    class CeleryConfig:
        broker_url = 'redis://redis:6379/0'
        result_backend = 'redis://redis:6379/0'

init:
  loadExamples: false
  createAdmin: true
  adminUser:
    username: admin
    password: admin123
```

```bash
# Deploy with Helm
helm repo add superset https://apache.github.io/superset
helm install superset superset/superset -f values.yaml -n superset --create-namespace
```

### Bài tập

1. **Custom viz** — Build simple custom chart plugin (React + D3)
2. **Embedded** — Embed dashboard trong Next.js/React app với guest tokens
3. **RBAC** — Setup row-level security cho multi-tenant data
4. **K8s deploy** — Deploy Superset trên Kubernetes với Helm, configure Celery workers

### Resources

- 📖 [Custom Viz Plugins](https://superset.apache.org/docs/contributing/creating-viz-plugins)
- 📖 [Embedded SDK](https://www.npmjs.com/package/@superset-ui/embedded-sdk)
- 📖 [Security & Access](https://superset.apache.org/docs/security)
- 📖 [Helm Chart](https://github.com/apache/superset/tree/master/helm/superset)
- 💻 [Preset (managed Superset)](https://preset.io/)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **Superset** | Open-source BI | SQL Lab, flexible, embeddable | Complex deploy, less polished UI |
| **Metabase** | Open-source BI | Easy setup, beautiful UI, no SQL needed | Less flexible cho power users |
| **Looker Studio** | Free SaaS | Zero setup, Google ecosystem | Limited customization |
| **Redash** | Open-source | Lightweight, SQL-focused | Limited viz, less maintained |
| **Lightdash** | dbt-native BI | dbt metrics, version control | Newer, smaller ecosystem |

### Khi nào chọn gì?

- **Superset** → Cần SQL Lab power, custom viz, embedded analytics, large org
- **Metabase** → Quick setup, non-technical users, embedded analytics đơn giản
- **Looker Studio** → Free, Google ecosystem, basic reporting
- **Redash** → Lightweight, SQL-only team, simple dashboards
- **Lightdash** → dbt-first team, metrics layer, version-controlled BI
