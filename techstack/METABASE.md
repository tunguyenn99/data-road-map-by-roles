# 💎 Metabase — Learning Guide (Beginner → Advanced)

## Tổng quan

**Metabase** là open-source BI tool nổi tiếng với minimal setup và UX thân thiện. Cho phép business users tự tạo reports mà không cần SQL, đồng thời power users vẫn có SQL editor mạnh mẽ.

**Use cases:**
- Self-service analytics cho non-technical users
- Internal dashboards & reporting
- Embedded analytics trong SaaS products
- Quick data exploration

**Ai dùng:** Data Analysts, Product Managers, Business Users, Developers (embedded)

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Question** | Một query/visualization (GUI hoặc SQL) |
| **Dashboard** | Collection of questions + filters |
| **Collection** | Folder-like organization cho questions/dashboards |
| **Model** | Curated dataset (like a virtual table) |
| **Pulse** | Scheduled report delivery (email/Slack) |
| **Filter** | Interactive parameter cho dashboard |

### Docker Install

```bash
# Quickest start
docker run -d -p 3000:3000 --name metabase metabase/metabase

# With persistent storage
docker run -d -p 3000:3000 \
  -v metabase-data:/metabase.db \
  --name metabase \
  metabase/metabase

# Access at http://localhost:3000
# Setup wizard: create admin account, connect database
```

```yaml
# docker-compose.yml (with PostgreSQL backend)
version: "3"
services:
  metabase:
    image: metabase/metabase:latest
    ports:
      - "3000:3000"
    environment:
      MB_DB_TYPE: postgres
      MB_DB_DBNAME: metabase
      MB_DB_PORT: 5432
      MB_DB_USER: metabase
      MB_DB_PASS: metabase_password
      MB_DB_HOST: postgres
    depends_on:
      - postgres
  
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: metabase
      POSTGRES_USER: metabase
      POSTGRES_PASSWORD: metabase_password
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### Ask Questions (GUI Mode)

```
GUI Query Builder (không cần SQL):
1. New > Question
2. Pick your data: chọn table
3. Filter: Add conditions (date range, category = "Electronics")
4. Summarize: COUNT, SUM, AVG (group by columns)
5. Sort: Order results
6. Visualize: Auto-picks best chart type

Ví dụ:
- "Orders" table
- Filter: created_at is in the last 30 days
- Summarize: Count, grouped by Product Category
- Visualize: Bar chart
```

### Ask Questions (SQL Mode)

```sql
-- Native query (SQL)
SELECT
  DATE_TRUNC('week', created_at) AS week,
  product_category,
  COUNT(*) AS order_count,
  SUM(total_amount) AS revenue
FROM orders
WHERE created_at >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY 1, 2
ORDER BY 1, 4 DESC;

-- With variables (user can input)
SELECT *
FROM customers
WHERE country = {{country}}
  AND created_at >= {{start_date}}
  AND created_at <= {{end_date}}
-- {{country}} creates a filter widget automatically
```

### Bài tập

1. **Docker setup** — Cài Metabase, kết nối database (PostgreSQL/MySQL)
2. **GUI questions** — Tạo 5 questions không dùng SQL (GUI mode)
3. **SQL questions** — Tạo 3 questions bằng native SQL với variables
4. **Dashboard** — Gộp questions vào dashboard, thêm date filter

### Resources

- 📖 [Metabase Docs](https://www.metabase.com/docs/latest/)
- 📖 [Getting Started](https://www.metabase.com/docs/latest/getting-started)
- 💻 [Metabase GitHub](https://github.com/metabase/metabase)
- 🎬 [Metabase YouTube](https://www.youtube.com/@metaborase)


---

## Level 2: Intermediate (2–4 tuần)

### Models

```
Models = curated, reusable datasets (like dbt models in BI):

1. Save SQL question as Model:
   - Write SQL defining clean dataset
   - Click "..." > "Turn into a model"
   - Add metadata: column descriptions, types, visibility

2. Benefits:
   - Shows up in data picker as a "table"
   - Business users explore Models instead of raw tables
   - Abstraction layer between raw DB and end users

Ví dụ Model:
```

```sql
-- Model: "Active Customers"
SELECT
  c.id AS customer_id,
  c.name,
  c.email,
  c.country,
  c.signup_date,
  COUNT(o.id) AS total_orders,
  SUM(o.amount) AS lifetime_value,
  MAX(o.created_at) AS last_order_date,
  CASE
    WHEN MAX(o.created_at) >= CURRENT_DATE - INTERVAL '30 days' THEN 'Active'
    WHEN MAX(o.created_at) >= CURRENT_DATE - INTERVAL '90 days' THEN 'At Risk'
    ELSE 'Churned'
  END AS status
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
GROUP BY c.id, c.name, c.email, c.country, c.signup_date
```

### Custom Columns & Expressions

```
Dashboard Filter + Custom Column:

Custom columns (in GUI query builder):
- Concatenate: concat([First Name], " ", [Last Name])
- Conditional: case([Status] = "active", "✅", "❌")
- Math: [Revenue] / [Orders] (average order value)
- Date: datetimeAdd([Created At], 30, "day")
- Extract: year([Created At]), month([Created At])
```

### Dashboard Filters & Subscriptions

```
Advanced Filters:
1. Dashboard > Edit > Add Filter
2. Types:
   - Time: Date range picker
   - Location: Country/City picker
   - ID: Specific record lookup
   - Text/Number: Free input
3. Wire filters to multiple cards
4. Default values: set sensible defaults
5. Required: mark critical filters as required

Subscriptions (email/Slack):
1. Dashboard > "..." > Subscriptions
2. Schedule: daily/weekly/monthly
3. Recipients: email list or Slack channel
4. Conditions: "Send if results exist" (avoid empty reports)
5. Filter values: pin specific filter values for each subscription
```

### Embedding

```html
<!-- Public embed (no auth required) -->
<!-- Dashboard > Sharing > Public link -->
<iframe
  src="https://metabase.your-company.com/public/dashboard/UUID"
  frameborder="0"
  width="100%"
  height="600"
  allowtransparency>
</iframe>

<!-- Signed embed (with parameters) -->
<!-- Requires JWT token from backend -->
```

```python
# Backend: generate signed embed URL
import jwt
import time

METABASE_SITE_URL = "https://metabase.your-company.com"
METABASE_SECRET_KEY = "your-embedding-secret"

payload = {
    "resource": {"dashboard": 1},
    "params": {
        "customer_id": 42  # Lock filter to specific customer
    },
    "exp": round(time.time()) + (10 * 60)  # 10 min expiry
}

token = jwt.encode(payload, METABASE_SECRET_KEY, algorithm="HS256")
iframe_url = f"{METABASE_SITE_URL}/embed/dashboard/{token}#bordered=true&titled=true"
```

### Bài tập

1. **Models** — Tạo 3 Models từ complex SQL, add metadata, dùng trong GUI
2. **Custom columns** — Tạo questions với custom columns + expressions
3. **Subscriptions** — Setup weekly email subscription cho dashboard
4. **Embedding** — Embed dashboard (public + signed) trong simple HTML page

### Resources

- 📖 [Models Documentation](https://www.metabase.com/docs/latest/data-modeling/models)
- 📖 [Custom Expressions](https://www.metabase.com/docs/latest/questions/query-builder/expressions)
- 📖 [Dashboard Filters](https://www.metabase.com/docs/latest/dashboards/filters)
- 📖 [Embedding Guide](https://www.metabase.com/docs/latest/embedding/introduction)

---

## Level 3: Advanced (4–8 tuần)

### Metabase API

```python
import requests

METABASE_URL = "http://localhost:3000"
# Authenticate
session = requests.post(
    f"{METABASE_URL}/api/session",
    json={"username": "admin@company.com", "password": "password123"}
)
token = session.json()["id"]
headers = {"X-Metabase-Session": token}

# List all dashboards
dashboards = requests.get(
    f"{METABASE_URL}/api/dashboard",
    headers=headers
).json()

# Create a question programmatically
new_question = requests.post(
    f"{METABASE_URL}/api/card",
    headers=headers,
    json={
        "name": "Monthly Revenue",
        "dataset_query": {
            "type": "native",
            "native": {
                "query": "SELECT DATE_TRUNC('month', created_at) as month, SUM(amount) as revenue FROM orders GROUP BY 1"
            },
            "database": 1
        },
        "display": "line",
        "visualization_settings": {
            "graph.x_axis.title_text": "Month",
            "graph.y_axis.title_text": "Revenue"
        },
        "collection_id": 5
    }
).json()

# Export dashboard as PDF
pdf = requests.post(
    f"{METABASE_URL}/api/dashboard/1/pdf",
    headers=headers
)
with open("dashboard.pdf", "wb") as f:
    f.write(pdf.content)
```

### White-labeling (Pro/Enterprise)

```python
# Environment variables for white-labeling
MB_APPLICATION_NAME = "My Analytics"
MB_APPLICATION_FAVICON_URL = "https://company.com/favicon.ico"
MB_APPLICATION_LOGO_URL = "https://company.com/logo.svg"
MB_APPLICATION_COLORS = '{"brand":"#FF5733","filter":"#2ECC71"}'
MB_LOADING_MESSAGE = "Loading your analytics..."
MB_SHOW_METABASE_LINKS = "false"

# In docker-compose.yml
environment:
  MB_APPLICATION_NAME: "Company Analytics Portal"
  MB_LOADING_MESSAGE: "Preparing your data..."
  MB_SHOW_METABASE_LINKS: "false"
```

### Custom Database Drivers

```clojure
;; Custom driver (Clojure) — for unsupported databases
;; plugins/my-driver/src/metabase/driver/my_driver.clj
(ns metabase.driver.my-driver
  (:require [metabase.driver :as driver]
            [metabase.driver.sql-jdbc :as sql-jdbc]))

(driver/register! :my-driver
  :parent :sql-jdbc)

(defmethod sql-jdbc/connection-details->spec :my-driver
  [_ {:keys [host port db user password]}]
  {:classname "com.mydb.Driver"
   :subprotocol "mydb"
   :subname (format "//%s:%d/%s" host port db)
   :user user
   :password password})
```

### Kubernetes Deployment

```yaml
# metabase-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: metabase
  namespace: analytics
spec:
  replicas: 2
  selector:
    matchLabels:
      app: metabase
  template:
    metadata:
      labels:
        app: metabase
    spec:
      containers:
        - name: metabase
          image: metabase/metabase:v0.48.0
          ports:
            - containerPort: 3000
          env:
            - name: MB_DB_TYPE
              value: postgres
            - name: MB_DB_HOST
              valueFrom:
                secretKeyRef:
                  name: metabase-secrets
                  key: db-host
            - name: MB_DB_DBNAME
              value: metabase
            - name: MB_DB_USER
              valueFrom:
                secretKeyRef:
                  name: metabase-secrets
                  key: db-user
            - name: MB_DB_PASS
              valueFrom:
                secretKeyRef:
                  name: metabase-secrets
                  key: db-pass
            - name: JAVA_OPTS
              value: "-Xmx2g"
          resources:
            requests:
              cpu: "500m"
              memory: "1Gi"
            limits:
              cpu: "2000m"
              memory: "3Gi"
          readinessProbe:
            httpGet:
              path: /api/health
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10
---
apiVersion: v1
kind: Service
metadata:
  name: metabase
  namespace: analytics
spec:
  selector:
    app: metabase
  ports:
    - port: 80
      targetPort: 3000
  type: ClusterIP
```

### Bài tập

1. **API automation** — Script tạo 10 questions + dashboard qua API
2. **White-label** — Customize branding (logo, colors, application name)
3. **K8s deploy** — Deploy Metabase HA trên Kubernetes
4. **Permissions** — Setup group-based permissions (data sandbox per team)

### Resources

- 📖 [Metabase API Reference](https://www.metabase.com/docs/latest/api-documentation)
- 📖 [Embedding SDK](https://www.metabase.com/docs/latest/embedding/sdk/introduction)
- 📖 [Enterprise Features](https://www.metabase.com/docs/latest/paid-features)
- 📖 [Kubernetes Deploy](https://www.metabase.com/docs/latest/installation-and-operation/running-metabase-on-kubernetes)
- 💻 [Metabase GitHub](https://github.com/metabase/metabase)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **Metabase** | Open-source BI | Easy setup, beautiful UI, embedding | Less flexible viz, Clojure codebase |
| **Superset** | Open-source BI | SQL Lab, custom viz, scalable | Complex setup |
| **Redash** | Open-source | Lightweight, SQL-focused, simple | Limited viz, maintenance mode |
| **Looker Studio** | Free SaaS | Zero setup, Google ecosystem | Limited customization |
| **Lightdash** | dbt-native BI | dbt metrics, git-based | Newer, smaller ecosystem |

### Khi nào chọn gì?

- **Metabase** → Non-technical users, quick setup, embedded analytics, beautiful defaults
- **Superset** → Power users, SQL Lab, custom visualizations, large scale
- **Redash** → SQL-only team, lightweight, simple dashboards
- **Looker Studio** → Free, Google ecosystem, basic reporting
- **Lightdash** → dbt-first workflow, metrics in code, version-controlled BI
