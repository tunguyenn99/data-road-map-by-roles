# 📈 Google Analytics 4 (GA4) — Learning Guide (Beginner → Advanced)

## Tổng quan

**Google Analytics 4** là web/app analytics platform của Google, thay thế Universal Analytics. GA4 dùng event-based model (mọi interaction = event), hỗ trợ cross-platform tracking (web + app), và tích hợp mạnh với BigQuery.

**Use cases:**
- Website/app traffic analysis
- User behavior tracking
- Conversion optimization
- Marketing attribution
- Product analytics

**Ai dùng:** Data Analysts, Marketing Analysts, Product Managers, Growth Engineers

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Event** | Mọi user interaction (page_view, click, purchase) |
| **Parameter** | Metadata đính kèm event (page_title, value) |
| **User** | Unique visitor (identified by cookie/user_id) |
| **Session** | Group of events trong 1 visit |
| **Conversion** | Key event bạn muốn track (purchase, signup) |
| **Data Stream** | Source of data (website, iOS app, Android app) |
| **Property** | Container cho streams + settings |

### Setup Property & Data Stream

```
1. Go to: https://analytics.google.com
2. Admin > Create Property
3. Property name: "My Website"
4. Time zone: Asia/Ho_Chi_Minh
5. Currency: VND

6. Create Data Stream:
   - Platform: Web
   - URL: https://your-website.com
   - Stream name: "Main Website"
   - Enhanced Measurement: ON (auto-tracks scrolls, outbound clicks, etc.)

7. Get Measurement ID: G-XXXXXXXXXX
```

### Install GA4 Tag

```html
<!-- Option 1: gtag.js (direct) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>

<!-- Option 2: Google Tag Manager (recommended) -->
<!-- Install GTM container, then add GA4 Configuration tag -->
```

### Basic Reports

```
Reports overview:
1. Realtime — Live user activity
2. Acquisition — How users find you (channels, campaigns)
3. Engagement — What users do (pages, events, conversions)
4. Monetization — Revenue, purchases, ads
5. Retention — User return rates

Key metrics:
- Users (total, new, returning)
- Sessions
- Engagement rate (vs bounce rate)
- Average engagement time
- Conversions
- Revenue
```

### Explorations (Custom Reports)

```
Explore > Create new exploration:

Free-form exploration:
  - Dimensions: Page path, Country, Device category
  - Metrics: Active users, Sessions, Engagement rate
  - Filter: Last 30 days
  - Visualization: Table, Bar chart, Line chart

Funnel exploration:
  - Step 1: page_view (landing page)
  - Step 2: add_to_cart
  - Step 3: begin_checkout
  - Step 4: purchase
  - Shows drop-off at each step

Path exploration:
  - Starting point: session_start
  - Shows user navigation paths
  - Identify popular page flows
```

### Bài tập

1. **Setup GA4** — Tạo property, add data stream, install tag on test site
2. **Explore reports** — Navigate all standard reports, understand metrics
3. **Exploration** — Build funnel exploration cho conversion flow
4. **Events** — Xem auto-collected events, understand event parameters

### Resources

- 📖 [GA4 Documentation](https://developers.google.com/analytics)
- 📖 [GA4 Setup Guide](https://support.google.com/analytics/answer/9304153)
- 🎬 [Google Analytics YouTube](https://www.youtube.com/c/googleanalytics)
- 📖 [GA4 Skill Shop (Google free course)](https://skillshop.google.com/analytics)

---

## Level 2: Intermediate (2–4 tuần)

### Custom Events

```javascript
// Track custom events with gtag.js
// Event: user signs up
gtag('event', 'sign_up', {
  method: 'email',
  user_type: 'free'
});

// Event: add to cart
gtag('event', 'add_to_cart', {
  currency: 'VND',
  value: 500000,
  items: [{
    item_id: 'SKU_123',
    item_name: 'Wireless Mouse',
    item_category: 'Electronics',
    price: 500000,
    quantity: 1
  }]
});

// Event: purchase
gtag('event', 'purchase', {
  transaction_id: 'T_12345',
  value: 1500000,
  currency: 'VND',
  shipping: 30000,
  items: [
    { item_id: 'SKU_123', item_name: 'Wireless Mouse', price: 500000, quantity: 1 },
    { item_id: 'SKU_456', item_name: 'Keyboard', price: 1000000, quantity: 1 }
  ]
});

// Custom event with custom parameters
gtag('event', 'search', {
  search_term: 'laptop gaming',
  results_count: 42
});
```

### Audiences

```
Admin > Data Display > Audiences:

Example audiences:
1. "High-value customers"
   - Condition: purchase events where value > 1,000,000 VND
   - In last 30 days

2. "Cart abandoners"
   - Condition: add_to_cart event happened
   - AND purchase did NOT happen
   - In last 7 days

3. "Engaged readers"
   - Condition: engagement_time_msec > 120000 (2 min)
   - AND page_view count > 5
   - In last 14 days

Use audiences for:
- Google Ads remarketing
- Analysis (compare segments)
- Personalization
```

### BigQuery Export

```
Admin > Data Streams > BigQuery Linking:
1. Link to BigQuery project
2. Choose: Streaming (real-time) or Daily export
3. Data appears in: analytics_<property_id>.events_*

BigQuery table structure:
- events_YYYYMMDD (daily tables)
- events_intraday_YYYYMMDD (streaming, today)
```

```sql
-- Query GA4 data in BigQuery
-- Daily active users
SELECT
  COUNT(DISTINCT user_pseudo_id) AS dau,
  event_date
FROM `project.analytics_123456789.events_*`
WHERE _TABLE_SUFFIX BETWEEN '20240101' AND '20240131'
  AND event_name = 'session_start'
GROUP BY event_date
ORDER BY event_date;

-- Funnel analysis
WITH funnel AS (
  SELECT
    user_pseudo_id,
    MAX(IF(event_name = 'page_view', 1, 0)) AS viewed,
    MAX(IF(event_name = 'add_to_cart', 1, 0)) AS added_to_cart,
    MAX(IF(event_name = 'begin_checkout', 1, 0)) AS began_checkout,
    MAX(IF(event_name = 'purchase', 1, 0)) AS purchased
  FROM `project.analytics_123456789.events_20240101`
  GROUP BY user_pseudo_id
)
SELECT
  COUNT(*) AS total_users,
  SUM(viewed) AS step1_view,
  SUM(added_to_cart) AS step2_cart,
  SUM(began_checkout) AS step3_checkout,
  SUM(purchased) AS step4_purchase
FROM funnel;

-- Extract event parameters
SELECT
  event_timestamp,
  user_pseudo_id,
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'page_title') AS page_title,
  (SELECT value.int_value FROM UNNEST(event_params) WHERE key = 'engagement_time_msec') AS engagement_ms
FROM `project.analytics_123456789.events_20240115`
WHERE event_name = 'page_view'
LIMIT 100;
```

### Looker Studio Integration

```
1. Looker Studio > New Report > Add Data
2. Choose "Google Analytics" connector
3. Select GA4 property
4. Build dashboard:
   - Scorecard: Users, Sessions, Conversions
   - Time series: Daily users trend
   - Geo map: Users by country
   - Table: Top pages by views
   - Funnel: Conversion funnel
```

### Bài tập

1. **Custom events** — Implement 5 custom events (signup, search, download, share, error)
2. **Audiences** — Create 3 audiences, use in Explorations to compare
3. **BigQuery export** — Enable export, write 5 SQL queries on GA4 data
4. **Dashboard** — Build Looker Studio dashboard from GA4 data

### Resources

- 📖 [Custom Events Guide](https://developers.google.com/analytics/devguides/collection/ga4/events)
- 📖 [BigQuery Export Schema](https://support.google.com/analytics/answer/7029846)
- 📖 [Audiences](https://support.google.com/analytics/answer/9267572)
- 📖 [GA4 + BigQuery Cookbook](https://developers.google.com/analytics/bigquery)

---

## Level 3: Advanced (4–8 tuần)

### Measurement Protocol

```python
# Server-side event tracking (bypass ad-blockers)
import requests
import json

MEASUREMENT_ID = "G-XXXXXXXXXX"
API_SECRET = "your-api-secret"  # Admin > Data Streams > Measurement Protocol

def send_event(client_id: str, events: list[dict]):
    """Send events via Measurement Protocol."""
    url = f"https://www.google-analytics.com/mp/collect?measurement_id={MEASUREMENT_ID}&api_secret={API_SECRET}"
    
    payload = {
        "client_id": client_id,
        "events": events
    }
    
    response = requests.post(url, json=payload)
    return response.status_code

# Track server-side purchase
send_event(
    client_id="client_123",
    events=[{
        "name": "purchase",
        "params": {
            "transaction_id": "T_001",
            "value": 1500000,
            "currency": "VND",
            "items": [
                {"item_id": "SKU_001", "item_name": "Product A", "quantity": 2, "price": 750000}
            ]
        }
    }]
)

# Track offline conversion
send_event(
    client_id="client_456",
    events=[{
        "name": "offline_purchase",
        "params": {
            "value": 5000000,
            "currency": "VND",
            "store_id": "STORE_HCM_01"
        }
    }]
)
```

### GA4 Data API (Reporting)

```python
# pip install google-analytics-data
from google.analytics.data_v1beta import BetaAnalyticsDataClient
from google.analytics.data_v1beta.types import (
    RunReportRequest, Dimension, Metric, DateRange, FilterExpression, Filter
)

client = BetaAnalyticsDataClient()
PROPERTY_ID = "123456789"

# Basic report
request = RunReportRequest(
    property=f"properties/{PROPERTY_ID}",
    dimensions=[
        Dimension(name="date"),
        Dimension(name="country"),
    ],
    metrics=[
        Metric(name="activeUsers"),
        Metric(name="sessions"),
        Metric(name="conversions"),
    ],
    date_ranges=[DateRange(start_date="30daysAgo", end_date="today")],
    dimension_filter=FilterExpression(
        filter=Filter(
            field_name="country",
            string_filter=Filter.StringFilter(value="Vietnam")
        )
    )
)

response = client.run_report(request)

for row in response.rows:
    date = row.dimension_values[0].value
    country = row.dimension_values[1].value
    users = row.metric_values[0].value
    sessions = row.metric_values[1].value
    print(f"{date} | {country} | Users: {users} | Sessions: {sessions}")
```

```javascript
// GA4 API with Node.js
const { BetaAnalyticsDataClient } = require('@google-analytics/data');

const analyticsDataClient = new BetaAnalyticsDataClient();
const propertyId = '123456789';

async function runReport() {
  const [response] = await analyticsDataClient.runReport({
    property: `properties/${propertyId}`,
    dateRanges: [{ startDate: '7daysAgo', endDate: 'today' }],
    dimensions: [{ name: 'pagePath' }],
    metrics: [{ name: 'screenPageViews' }, { name: 'averageSessionDuration' }],
    orderBys: [{ metric: { metricName: 'screenPageViews' }, desc: true }],
    limit: 20,
  });
  
  response.rows.forEach(row => {
    console.log(`${row.dimensionValues[0].value}: ${row.metricValues[0].value} views`);
  });
}
```

### Attribution Modeling

```sql
-- BigQuery: Custom attribution analysis
-- Data-driven attribution (comparing to last-click)

WITH user_journeys AS (
  SELECT
    user_pseudo_id,
    event_timestamp,
    (SELECT value.string_value FROM UNNEST(event_params) 
     WHERE key = 'source') AS traffic_source,
    (SELECT value.string_value FROM UNNEST(event_params) 
     WHERE key = 'medium') AS traffic_medium,
    event_name,
    (SELECT value.double_value FROM UNNEST(event_params) 
     WHERE key = 'value') AS purchase_value
  FROM `project.analytics_123456789.events_*`
  WHERE _TABLE_SUFFIX BETWEEN '20240101' AND '20240131'
),

conversion_paths AS (
  SELECT
    user_pseudo_id,
    ARRAY_AGG(
      STRUCT(traffic_source, traffic_medium, event_timestamp)
      ORDER BY event_timestamp
    ) AS touchpoints,
    MAX(IF(event_name = 'purchase', purchase_value, 0)) AS conversion_value
  FROM user_journeys
  WHERE traffic_source IS NOT NULL
  GROUP BY user_pseudo_id
)

-- First-touch attribution
SELECT
  touchpoints[OFFSET(0)].traffic_source AS first_touch_source,
  COUNT(DISTINCT user_pseudo_id) AS users,
  SUM(conversion_value) AS attributed_revenue
FROM conversion_paths
WHERE conversion_value > 0
GROUP BY 1
ORDER BY 3 DESC;
```

### Predictive Audiences

```
GA4 Predictive Metrics (auto-generated by ML):
1. Purchase probability — likelihood to purchase in next 7 days
2. Churn probability — likelihood to NOT visit in next 7 days
3. Revenue prediction — predicted revenue in next 28 days

Requirements:
- 1000+ returning users who triggered condition in 7 days
- 1000+ who did NOT trigger
- Min 28 days of data

Create predictive audience:
- Admin > Audiences > New Audience
- Template: "Likely 7-day purchasers"
- Condition: Purchase probability > 90th percentile
- Use for: Google Ads targeting, personalization
```

### Bài tập

1. **Measurement Protocol** — Send 100 server-side events, verify in Realtime report
2. **Data API** — Build Python script generate weekly report (PDF/email)
3. **Attribution** — BigQuery analysis comparing first-touch vs last-touch vs linear
4. **Predictive** — Set up predictive audiences, export to Google Ads

### Resources

- 📖 [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/ga4)
- 📖 [Data API Reference](https://developers.google.com/analytics/devguides/reporting/data/v1)
- 📖 [BigQuery Export Guide](https://support.google.com/analytics/answer/9358801)
- 📖 [Predictive Audiences](https://support.google.com/analytics/answer/9846734)
- 💻 [GA4 Python Samples](https://github.com/googleanalytics/python-docs-samples)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **GA4** | Free analytics | Free, Google ecosystem, BigQuery export | Sampling, data ownership |
| **Mixpanel** | Product analytics | Event-based, powerful funnels, retention | Paid, focused on product |
| **Amplitude** | Product analytics | Behavioral cohorts, strong segmentation | Paid, complex |
| **PostHog** | Open-source | Self-host, session replay, feature flags | Setup complexity |
| **Matomo** | Open-source | GDPR-friendly, self-host, GA-like | Less features, self-maintain |
| **Adobe Analytics** | Enterprise | Most powerful, custom vars | Very expensive, complex |

### Khi nào chọn gì?

- **GA4** → Free tier đủ, Google Ads user, cần BigQuery integration
- **Mixpanel** → Product-focused team, funnel/retention analysis, SaaS products
- **Amplitude** → Large product team, behavioral analytics, experimentation
- **PostHog** → Self-hosted preference, all-in-one (analytics + replay + flags)
- **Matomo** → GDPR compliance priority, self-hosted, privacy-first
- **Adobe Analytics** → Enterprise, unlimited custom variables, complex attribution
