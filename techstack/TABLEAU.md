# 📉 Tableau — Learning Guide (Beginner → Advanced)

## Tổng quan

Tableau là BI/visualization platform mạnh, nổi tiếng về khả năng tạo visualizations đẹp và tương tác cao. Phổ biến ở MNCs, e-commerce, tech companies tại VN.

**Dùng cho:** DA, BI Analyst/Developer  
**Certifications:** Tableau Desktop Specialist, Tableau Certified Data Analyst

---

## Level 1: Beginner (2 tuần)

### Core Concepts
- Worksheets, Dashboards, Stories
- Dimensions (categorical) vs Measures (numerical)
- Discrete (blue) vs Continuous (green)
- Marks: color, size, shape, detail, tooltip
- Show Me panel: auto-suggest chart types

### First Visualization
1. Connect to data source (Excel, CSV, database)
2. Drag dimension to Columns, measure to Rows
3. Choose chart type (Show Me or manual)
4. Add filters, colors, labels
5. Create dashboard: combine worksheets

### Basic Calculations
```
// Calculated fields
Profit Ratio = SUM([Profit]) / SUM([Sales])
Order Year = YEAR([Order Date])
Customer Segment = IF [Revenue] > 10000 THEN "High" ELSE "Low" END
```

### Bài tập
1. Connect CSV, create bar chart (revenue by region)
2. Create line chart (monthly trend)
3. Build scatter plot (correlation)
4. Create 1 dashboard with 4 charts
5. Publish to Tableau Public

### Resources
| Resource | Link | Type |
|---|---|---|
| Tableau Free Training | [tableau.com/learn](https://www.tableau.com/learn/training) | Free |
| Tableau Public Gallery | [public.tableau.com](https://public.tableau.com/app/discover) | Free |
| Tableau Desktop Specialist Prep | [tableau.com/learn/certification](https://www.tableau.com/learn/certification/desktop-specialist) | Paid |

---

## Level 2: Intermediate (3 tuần)

### Table Calculations
```
// Running total
RUNNING_SUM(SUM([Sales]))

// Percent of total
SUM([Sales]) / TOTAL(SUM([Sales]))

// Year-over-Year growth
(ZN(SUM([Sales])) - LOOKUP(ZN(SUM([Sales])), -1)) / ABS(LOOKUP(ZN(SUM([Sales])), -1))

// Moving average
WINDOW_AVG(SUM([Sales]), -6, 0)
```

### LOD Expressions (Level of Detail)
```
// FIXED: compute at specific granularity
{ FIXED [Customer ID] : SUM([Sales]) }  // Customer lifetime value

// INCLUDE: add detail
{ INCLUDE [Order ID] : AVG([Discount]) }

// EXCLUDE: remove detail
{ EXCLUDE [Region] : SUM([Sales]) }  // Total without region
```

### Parameters & Sets
```
// Parameter: user-selectable value
// Create parameter "Top N" (integer, range 1-20)
// Use in filter: RANK(SUM([Sales])) <= [Top N Parameter]

// Sets: define subsets
// Top 10 customers by revenue
// Then use IN/OUT for comparison
```

### Dashboard Design
- Layout containers (horizontal, vertical)
- Device-specific layouts (desktop, tablet, mobile)
- Actions: filter, highlight, URL, parameter, set
- Tooltips: Viz in Tooltip (embedded chart)
- Navigation buttons

### Bài tập
1. Create LOD: customer-level metrics on order-level data
2. Build parameter-driven dashboard (dynamic top N)
3. Implement drill-down: region → city → store
4. Create action filters across 3 worksheets
5. Publish to Tableau Public with 3 interacting views

### Resources
| Resource | Link | Type |
|---|---|---|
| Workout Wednesday | [workout-wednesday.com](https://workout-wednesday.com/) | Free |
| MakeoverMonday | [makeovermonday.co.uk](https://makeovermonday.co.uk/) | Free |
| LOD Expressions Guide | [tableau.com](https://www.tableau.com/about/blog/LOD-expressions) | Free |
| Tableau Prep Builder | [tableau.com/products/prep](https://www.tableau.com/products/prep) | Paid |

---

## Level 3: Advanced (4 tuần)

### Advanced Analytics
```
// R/Python integration
SCRIPT_REAL("
import numpy as np
return np.polyfit(_arg1, _arg2, 1)[0]
", SUM([Sales]), SUM([Profit]))

// Clustering (built-in)
// Drag cluster to detail mark

// Forecasting (built-in exponential smoothing)
// Analytics pane → Forecast
```

### Performance Optimization
- Extract vs Live connection
- Data source filters (reduce data early)
- Context filters (force evaluation order)
- Boolean calculated fields (faster than string)
- Minimize marks (aggregate before visualizing)
- Use FIXED LOD instead of INCLUDE (faster)

### Tableau Server / Cloud
- Publishing: workbooks, data sources
- Permissions: project → workbook → view
- Schedules: extract refresh, subscriptions
- Data governance: certified data sources
- Embedded analytics: Tableau Embedding API v3

### Tableau Prep
```
ETL in Tableau:
1. Input (connect to data)
2. Clean (rename, filter, split, merge)
3. Pivot / Unpivot
4. Join / Union
5. Aggregate
6. Output (publish to server/file)
```

### Bài tập
1. Build performance-optimized dashboard (<3s load)
2. Create embedded Tableau viz in web page
3. Build Tableau Prep flow: 3 sources → clean → join → output
4. Implement row-level security on Tableau Server
5. Tableau Certified Data Analyst practice exam

### Resources
| Resource | Link | Type |
|---|---|---|
| Tableau Server Admin | [help.tableau.com](https://help.tableau.com/current/server/en-us/admin.htm) | Free |
| Tableau Embedding API | [tableau.github.io](https://tableau.github.io/embedding-api-v3-guide/) | Free |
| The Big Book of Dashboards | Steve Wexler | Paid |
| Information Dashboard Design | Stephen Few | Paid |

---
## Alternatives & Công cụ tương đương

| Tool | vs Tableau | Best for |
|---|---|---|
| **Power BI** | Cheaper, Microsoft ecosystem | Banking/Enterprise VN |
| **Looker** | Code-first, better governance | Data-mature orgs |
| **Metabase** | Free, simpler | Startups, quick exploration |
| **Sigma Computing** | Spreadsheet-like BI | Business users who love Excel |
| **Mode Analytics** | SQL + Python + BI | Data teams who code |
| **Hex** | Notebook + BI hybrid | Modern data teams |

### Khi nào chọn gì?
- **Tableau** → Beautiful viz, exploration, MNCs
- **Power BI** → Cost-effective enterprise, Microsoft stack
- **Looker** → Governance-first, GCP
- **Mode/Hex** → Technical teams, code + viz
