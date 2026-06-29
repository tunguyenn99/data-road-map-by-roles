# 📊 Looker Studio — Learning Guide (Beginner → Advanced)

## Tổng quan

**Looker Studio** (trước đây là Google Data Studio) là công cụ BI/visualization miễn phí của Google. Cho phép kết nối nhiều data sources, tạo interactive reports và dashboards chia sẻ dễ dàng.

**Use cases:**
- Business dashboards & reporting
- Marketing analytics (GA4, Google Ads)
- Data visualization cho stakeholders
- Embedded analytics

**Ai dùng:** Data Analysts, Marketing Analysts, Business Users, Product Managers

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Data Source** | Kết nối tới data (BigQuery, Sheets, DB) |
| **Report** | Một document chứa charts và tables |
| **Page** | Một trang trong report |
| **Chart** | Visualization (bar, line, pie, table, map) |
| **Dimension** | Categorical field (country, product, date) |
| **Metric** | Numeric field (revenue, count, average) |
| **Calculated Field** | Custom field tạo bằng formula |
| **Filter** | Giới hạn data hiển thị |

### Connect Data Source

```
Bước 1: Mở https://lookerstudio.google.com
Bước 2: Create > Data Source
Bước 3: Chọn connector:
  - Google Sheets (dễ nhất để bắt đầu)
  - BigQuery
  - PostgreSQL / MySQL
  - Google Analytics 4
  - CSV Upload
Bước 4: Authorize & configure
Bước 5: Click "Connect"
```

### First Report

```
1. Create > Report
2. Add data source (Google Sheets / BigQuery)
3. Thêm charts:
   - Scorecard: Total Revenue
   - Time Series: Revenue over time
   - Bar Chart: Revenue by Category
   - Table: Top 10 Products
4. Add Date Range filter
5. Add dimension filter (e.g., Country dropdown)
6. Style: Theme > chọn color palette
```

### Basic Charts Configuration

```
Time Series Chart:
  - Dimension: Date (day/week/month)
  - Metric: SUM(revenue)
  - Comparison: Previous period
  - Style: Show trend line

Bar Chart:
  - Dimension: product_category
  - Metric: SUM(sales), COUNT(orders)
  - Sort: SUM(sales) DESC
  - Style: Stacked / Grouped

Geo Map:
  - Dimension: country (geo type)
  - Metric: COUNT(users)
  - Style: Filled map, color gradient
```

### Bài tập

1. **Google Sheets report** — Kết nối Sheet → tạo report 4 charts + filters
2. **BigQuery report** — Kết nối public dataset → time series + bar + table
3. **Styling** — Apply theme, custom colors, add logo, format numbers
4. **Share** — Share report (view/edit), schedule email delivery

### Resources

- 📖 [Looker Studio Help](https://support.google.com/looker-studio)
- 📖 [Getting Started Guide](https://support.google.com/looker-studio/answer/6283323)
- 🎬 [Google Analytics YouTube — Looker Studio](https://www.youtube.com/playlist?list=PLI5YfMzCfRtZY5FKpIMFNq8aR5dEQCpKi)
- 📖 [Report Gallery (templates)](https://lookerstudio.google.com/gallery)

---

## Level 2: Intermediate (2–4 tuần)

### Calculated Fields

```
-- Basic calculated field
Revenue_per_Order = SUM(revenue) / COUNT(order_id)

-- CASE statement
Customer_Segment = CASE
  WHEN total_spend > 10000 THEN "VIP"
  WHEN total_spend > 5000 THEN "Premium"
  ELSE "Standard"
END

-- Date functions
Days_Since_Last_Purchase = DATE_DIFF(TODAY(), last_purchase_date)

-- RegEx extract
Domain = REGEXP_EXTRACT(email, "@(.+)")

-- Conditional metric
Converted_Users = SUM(CASE WHEN event_name = "purchase" THEN 1 ELSE 0 END)

-- YoY comparison
Revenue_YoY_Change = (SUM(revenue) - SUM(revenue_last_year)) / SUM(revenue_last_year)
```

### Data Blending

```
Blend data từ 2+ sources:
1. Edit Report > Resource > Manage blends
2. Add data sources (left table + right table)
3. Configure Join:
   - Join key: date, user_id, etc.
   - Join type: LEFT, INNER, FULL OUTER, CROSS
4. Select dimensions & metrics from both tables

Ví dụ:
- Source 1: BigQuery (revenue data)
- Source 2: Google Sheets (targets/KPIs)
- Join on: month
- Chart: Actual vs Target comparison
```

### Parameters

```
-- Create parameter
Parameter: selected_metric (Text, list of values)
  Values: "Revenue", "Orders", "Users"

-- Use in calculated field
Dynamic_Metric = CASE
  WHEN selected_metric = "Revenue" THEN SUM(revenue)
  WHEN selected_metric = "Orders" THEN COUNT(order_id)
  WHEN selected_metric = "Users" THEN COUNT(DISTINCT user_id)
END

-- This allows users to switch metrics dynamically
```

### Community Connectors

```javascript
// Custom connector example (Apps Script)
function getConfig(request) {
  return {
    configParams: [
      {
        type: "TEXTINPUT",
        name: "apiEndpoint",
        displayName: "API Endpoint",
        helpText: "The URL of your API"
      }
    ]
  };
}

function getSchema(request) {
  return {
    schema: [
      { name: "date", label: "Date", dataType: "STRING", semantics: { conceptType: "DIMENSION" } },
      { name: "value", label: "Value", dataType: "NUMBER", semantics: { conceptType: "METRIC" } }
    ]
  };
}

function getData(request) {
  var url = request.configParams.apiEndpoint;
  var response = UrlFetchApp.fetch(url);
  var data = JSON.parse(response.getContentText());
  // Transform and return rows
  return { schema: getSchema().schema, rows: data.map(r => ({ values: [r.date, r.value] })) };
}
```

### Bài tập

1. **Calculated fields** — Tạo 5 calculated fields (CASE, date diff, regex, conditional)
2. **Data blending** — Blend BigQuery data với Google Sheets targets
3. **Parameters** — Tạo dynamic report user có thể switch metrics
4. **Interactive dashboard** — Drill-down từ overview → detail với page navigation

### Resources

- 📖 [Calculated Fields Reference](https://support.google.com/looker-studio/table/6379764)
- 📖 [Data Blending](https://support.google.com/looker-studio/answer/9061421)
- 📖 [Parameters](https://support.google.com/looker-studio/answer/9002005)
- 📖 [Community Connectors](https://developers.google.com/looker-studio/connector)

---

## Level 3: Advanced (4–8 tuần)

### Looker Studio Pro Features

```
Pro features (Google Workspace):
- Team workspaces (organize reports by team)
- Linked Looker Studio assets (shared data sources)
- Google Cloud admin controls
- SLA & support
- Scheduled delivery customization
- SAML / SSO integration
```

### Embedded Reports

```html
<!-- Embed report in website -->
<iframe
  width="100%"
  height="600"
  src="https://lookerstudio.google.com/embed/reporting/REPORT_ID/page/PAGE_ID"
  frameborder="0"
  style="border:0"
  allowfullscreen
  sandbox="allow-storage-access-by-user-activation allow-scripts allow-same-origin allow-popups allow-popups-to-escape-sandbox">
</iframe>

<!-- With URL parameters for filtering -->
<iframe
  src="https://lookerstudio.google.com/embed/reporting/REPORT_ID/page/PAGE_ID?params=%7B%22ds0.date_range%22:%222024-01-01%22%7D"
  width="100%"
  height="600">
</iframe>
```

### BigQuery BI Engine Integration

```sql
-- BI Engine: in-memory acceleration cho Looker Studio
-- Configure trong BigQuery Console:
-- 1. BigQuery > BI Engine > Create Reservation
-- 2. Chọn project + region + GB capacity
-- 3. Preferred tables (optional): specify tables to cache

-- Verify BI Engine usage
SELECT
  bi_engine_statistics
FROM `region-us`.INFORMATION_SCHEMA.JOBS_BY_PROJECT
WHERE creation_time > TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 1 HOUR)
  AND bi_engine_statistics IS NOT NULL;
```

### Link API (programmatic report management)

```python
# Looker Studio Link API — generate report links with pre-configured data sources
import urllib.parse

base_url = "https://lookerstudio.google.com/reporting/create"

params = {
    "c.reportId": "TEMPLATE_REPORT_ID",
    "r.reportName": "Sales Report - Q1 2024",
    "ds.ds0.connector": "bigQuery",
    "ds.ds0.projectId": "my-project",
    "ds.ds0.type": "TABLE",
    "ds.ds0.datasetId": "analytics",
    "ds.ds0.tableId": "daily_sales",
}

link = f"{base_url}?{urllib.parse.urlencode(params)}"
print(f"Report creation link: {link}")
```

### Bài tập

1. **Embedded analytics** — Embed report trong web app, pass filters via URL params
2. **BI Engine** — Configure BI Engine, measure query acceleration
3. **Link API** — Generate templatized reports programmatically
4. **Enterprise dashboard** — Multi-page dashboard với drill-down, parameters, blended data

### Resources

- 📖 [Looker Studio Pro](https://cloud.google.com/looker-studio)
- 📖 [Embedding Reports](https://support.google.com/looker-studio/answer/7450249)
- 📖 [Link API Reference](https://developers.google.com/looker-studio/integrate/linking-api)
- 📖 [BI Engine Overview](https://cloud.google.com/bigquery/docs/bi-engine-intro)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **Looker Studio** | Free BI | Free, Google ecosystem, easy share | Limited customization, slow |
| **Power BI** | Enterprise BI | DAX power, Microsoft ecosystem | License cost, Windows-centric |
| **Tableau** | Enterprise BI | Best visualizations, flexible | Expensive, heavy client |
| **Metabase** | Open-source BI | Self-hosted, easy, SQL-friendly | Less enterprise features |
| **Superset** | Open-source BI | Flexible, SQL Lab, plugins | Steeper setup, less polished |

### Khi nào chọn gì?

- **Looker Studio** → Google ecosystem, free tier đủ dùng, sharing dễ, non-technical users
- **Power BI** → Microsoft/Azure stack, complex DAX calculations, enterprise governance
- **Tableau** → Premium visualizations, complex analytics, large enterprise
- **Metabase** → Self-hosted, developer-friendly, quick setup, embedded analytics
- **Superset** → Open-source enterprise, custom viz cần thiết, SQL-heavy team
