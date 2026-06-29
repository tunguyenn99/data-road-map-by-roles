# 📊 Power BI — Learning Guide (Beginner → Advanced)

## Tổng quan

Microsoft Power BI là BI platform phổ biến nhất tại VN (đặc biệt banking/enterprise). Bao gồm: Power BI Desktop (build), Power BI Service (share), Power BI Mobile (consume).

**Dùng cho:** DA, BI Analyst/Developer  
**Certification:** Microsoft PL-300

---

## Level 1: Beginner (2 tuần)

### Core Concepts
- Dataset, Report, Dashboard, Workspace
- Power Query (ETL) vs DAX (calculations)
- Import mode vs DirectQuery
- Visual types: bar, line, pie, card, table, matrix, map

### First Dashboard
1. **Get Data**: connect to Excel/CSV/SQL
2. **Transform**: Power Query Editor (clean, rename, filter)
3. **Model**: create relationships between tables
4. **Visualize**: drag fields onto canvas, choose chart types
5. **Publish**: upload to Power BI Service

### Basic DAX
```dax
// Measures (always create measures, not calculated columns)
Total Revenue = SUM(Sales[Amount])
Order Count = COUNTROWS(Orders)
Average Order Value = DIVIDE([Total Revenue], [Order Count])

// Quick measures
YTD Revenue = TOTALYTD([Total Revenue], Calendar[Date])
```

### Bài tập
1. Connect to sample Excel file, build 3 visuals
2. Create relationships between 2 tables
3. Write 5 basic measures (SUM, COUNT, AVERAGE)
4. Design 1-page dashboard with cards + charts
5. Publish to Power BI Service

### Resources
| Resource | Link | Type |
|---|---|---|
| Power BI Guided Learning | [Microsoft Learn](https://learn.microsoft.com/en-us/power-bi/guided-learning/) | Free |
| Power BI Desktop Download | [powerbi.microsoft.com](https://powerbi.microsoft.com/en-us/downloads/) | Free |
| SQLBI Intro to DAX | [sqlbi.com](https://www.sqlbi.com/articles/introducing-dax/) | Free |
| Guy in a Cube (YouTube) | [youtube.com/@GuyInACube](https://www.youtube.com/@GuyInACube) | Free |

---

## Level 2: Intermediate (4 tuần)

### Data Modeling (Star Schema)
- Fact tables: transactional data (sales, orders)
- Dimension tables: descriptive data (customers, products, dates)
- Relationships: one-to-many, active/inactive
- Role-playing dimensions (e.g., order_date vs ship_date)

### DAX Intermediate
```dax
// Filter context
Revenue by Category = 
CALCULATE([Total Revenue], Products[Category] = "Electronics")

// Time intelligence
MoM Growth = 
VAR CurrentMonth = [Total Revenue]
VAR PreviousMonth = CALCULATE([Total Revenue], DATEADD(Calendar[Date], -1, MONTH))
RETURN DIVIDE(CurrentMonth - PreviousMonth, PreviousMonth)

// CALCULATE with multiple filters
Revenue High Value = 
CALCULATE(
    [Total Revenue],
    Customers[Segment] = "Premium",
    Calendar[Year] = 2024
)

// Iterator functions
Weighted Average Price = 
SUMX(Products, Products[Price] * Products[Quantity]) / SUM(Products[Quantity])

// ALL / REMOVEFILTERS
% of Total = DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL(Products)))
```

### Power Query (M Language)
```
// Custom column
= Table.AddColumn(Source, "FullName", each [FirstName] & " " & [LastName])

// Filter rows
= Table.SelectRows(Source, each [Status] = "Active")

// Group by
= Table.Group(Source, {"Region"}, {{"TotalRevenue", each List.Sum([Amount])}})

// Merge queries (JOIN)
= Table.NestedJoin(Orders, "CustomerID", Customers, "ID", "CustomerData", JoinKind.Left)
```

### Row-Level Security (RLS)
```dax
// Define RLS role: Branch Manager
[BranchCode] = USERPRINCIPALNAME()

// Or via security table
CONTAINS(SecurityTable, SecurityTable[Email], USERPRINCIPALNAME(), SecurityTable[Branch], Branches[BranchCode])
```

### Bài tập
1. Build star schema: 1 fact + 4 dimensions
2. Create time intelligence measures (YTD, MoM, QoQ)
3. Implement RLS for 3 roles
4. Use Power Query to merge 3 data sources
5. Create multi-page report with drill-through

### Resources
| Resource | Link | Type |
|---|---|---|
| DAX Patterns (SQLBI) | [daxpatterns.com](https://www.daxpatterns.com/) | Free |
| SQLBI courses | [sqlbi.com/courses](https://www.sqlbi.com/courses/) | Paid |
| Microsoft PL-300 Learning Path | [Microsoft Learn](https://learn.microsoft.com/en-us/certifications/power-bi-data-analyst-associate/) | Free |
| Curbal (YouTube) | [youtube.com/@CurbalEN](https://www.youtube.com/@CurbalEN) | Free |

---

## Level 3: Advanced (4 tuần)

### Performance Optimization
```dax
// Use variables to avoid repeated calculations
Revenue Growth = 
VAR _Current = [Total Revenue]
VAR _Previous = CALCULATE([Total Revenue], PREVIOUSMONTH(Calendar[Date]))
VAR _Growth = DIVIDE(_Current - _Previous, _Previous)
RETURN _Growth

// Avoid FILTER(), use KEEPFILTERS() or direct filter in CALCULATE
// Bad:
CALCULATE([Revenue], FILTER(ALL(Products), Products[Price] > 100))
// Better:
CALCULATE([Revenue], Products[Price] > 100)
```

### Composite Models & DirectQuery
- Import: fast but refresh needed
- DirectQuery: real-time but slower queries
- Composite: mix both (import dims + DQ facts)
- Aggregations: pre-aggregated tables for speed

### Deployment & Governance
```
Workspaces:
├── DEV (build & test)
├── UAT (business review)
└── PROD (consumption)

Deployment pipeline: DEV → UAT → PROD (one-click)
Git integration: .pbip files in version control
```

### Advanced Visuals
- Custom visuals (Charticulator, Deneb/Vega-Lite)
- Bookmarks + page navigation
- What-if parameters
- Field parameters (dynamic axis/measure switching)
- Paginated reports (RDL)

### Power BI Embedded
```javascript
// Embed in web app
var embedConfig = {
    type: 'report',
    id: reportId,
    embedUrl: embedUrl,
    accessToken: token
};
powerbi.embed(container, embedConfig);
```

### Bài tập
1. Optimize slow report: reduce from 30s to <5s
2. Set up deployment pipeline (3 workspaces)
3. Create composite model (Import + DirectQuery)
4. Build custom visual with Deneb
5. PL-300 mock exam: score ≥80%

### Resources
| Resource | Link | Type |
|---|---|---|
| DAX Studio | [daxstudio.org](https://daxstudio.org/) | Free |
| Tabular Editor | [tabulareditor.com](https://tabulareditor.com/) | Free/Paid |
| Power BI Performance Analyzer | Built-in | Free |
| PL-300 Practice Tests | [Microsoft Learn](https://learn.microsoft.com/en-us/certifications/practice-assessments-for-microsoft-certifications) | Free |
| The Definitive Guide to DAX (book) | Marco Russo & Alberto Ferrari | Paid |

---
## Alternatives & Công cụ tương đương

| Tool | Description | vs Power BI |
|---|---|---|
| **Tableau** | Best visualization, MNCs popular | More expensive, better for exploration |
| **Looker** | Code-first (LookML), Google Cloud | Better governance, steeper learning |
| **Metabase** | Open-source, simple | Quick setup, limited enterprise features |
| **Apache Superset** | Open-source, modern | Free, community-driven |
| **Holistics** | Code-based BI (Vietnamese company!) | Modeling layer, developer-friendly |
| **ThoughtSpot** | AI/NLQ-first BI | Search-based, expensive |
| **Lightdash** | Open-source, dbt-native | Built for dbt users |

### Khi nào chọn gì?
- **Power BI** → Microsoft ecosystem, banking/enterprise VN
- **Tableau** → MNCs, need best-in-class visualization
- **Looker** → GCP stack, code-first governance
- **Metabase/Superset** → Budget-friendly, startup
- **Lightdash** → dbt-native, AE teams
