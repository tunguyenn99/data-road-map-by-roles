# Career Roadmap: BI Engineer

## Bối cảnh

Roadmap dành cho thành viên đã có nền tảng DA (SQL vững, hiểu business) và muốn chuyển sang BI Engineer — vai trò tập trung vào visualization, data modeling cho reporting, và self-service analytics.

---

## Vai trò BI Engineer

| Aspect | Mô tả |
|--------|--------|
| **Mission** | Xây dựng hệ thống dashboards + data models phục vụ decision-making |
| **Collaborate with** | Business stakeholders, Data Analyst, AE, Management |
| **Output** | Dashboards, KPI definitions, dimensional models, semantic layer |
| **Tools** | Superset / QuickSight, SQL (Redshift), dbt (curated layer) |

### BI Engineer vs Data Analyst

| DA | BI Engineer |
|----|-------------|
| Ad-hoc queries & reports | Hệ thống dashboards production |
| Explore & answer questions | Design data models cho self-service |
| 1-time analysis | Recurring, automated, maintained |
| Trả lời "chuyện gì đang xảy ra?" | Trả lời "tôi muốn tự xem data bất kỳ lúc nào" |
| Output: insight | Output: product (dashboard = product) |

---

## Phase 1: BI Tool Mastery (Tuần 1-4)

**Mục tiêu:** Thành thạo ít nhất 1 BI tool, hiểu kiến trúc dashboard.

### Tools toys

| Tool | Dùng cho | Status |
|------|----------|--------|
| Apache Superset | Internal analytics, ad-hoc | Đang dùng |
| Amazon QuickSight | Management reporting, embedded | Đang triển khai |
| Metabase | Quick exploration (optional) | Evaluate |

### Skills

| Skill | Level cần đạt | Ghi chú |
|-------|:-------------:|---------|
| Dashboard creation | ⭐⭐⭐⭐ | Charts, filters, drill-down |
| Chart type selection | ⭐⭐⭐⭐ | Đúng chart cho đúng data |
| SQL in BI tool | ⭐⭐⭐⭐ | Custom queries, virtual datasets |
| Filters & parameters | ⭐⭐⭐⭐ | Dynamic filtering, cross-filter |
| Permissions & sharing | ⭐⭐⭐ | Row-level, role-based access |
| Performance tuning | ⭐⭐⭐ | Caching, query optimization |

### Chart Selection Guide

| Mục đích | Chart type |
|----------|-----------|
| Trend over time | Line chart, Area chart |
| Comparison | Bar chart (horizontal for many categories) |
| Composition | Stacked bar, Pie/Donut (≤5 categories) |
| Distribution | Histogram, Box plot |
| Relationship | Scatter plot |
| KPI highlight | Big Number, Gauge |
| Geographic | Map, Choropleth |
| Ranking | Horizontal bar (sorted) |

### Deliverables

- [ ] Tạo 3 dashboards trên Superset (complete với filters, drill-down)
- [ ] 1 dashboard trên QuickSight (management reporting)
- [ ] Document chart selection reasoning cho mỗi dashboard
- [ ] Performance < 5s load time cho tất cả dashboards

---

## Phase 2: Dimensional Modeling (Tuần 5-10)

**Mục tiêu:** Thiết kế data models tối ưu cho BI — star schema, SCD, aggregations.

### Concepts

| Concept | Mô tả | Ví dụ |
|---------|--------|-------------|
| Fact table | Bảng chứa events/metrics (số đo) | fact_orders, fact_subscriptions, fact_transactions |
| Dimension table | Bảng mô tả (thuộc tính) | dim_customer, dim_product, dim_users |
| Star schema | 1 fact + N dimensions | fact_orders → dim_customer, dim_store, dim_date |
| Snowflake | Dimension normalized thêm | dim_product → dim_category → dim_department |
| SCD Type 1 | Overwrite (không giữ history) | dim_customer.phone (update mới nhất) |
| SCD Type 2 | Giữ history (effective_from/to) | dim_customer_history (track thay đổi segment) |
| Degenerate dim | Dimension nằm trong fact | order_id trong fact_orders |
| Junk dimension | Gộp flags/indicators nhỏ | dim_flags (is_vip, is_staff, is_active) |
| Aggregate table | Pre-computed summaries | agg_daily_store_revenue |

### Star Schema Example (Ví dụ Star Schema E-commerce)

```
                    ┌──────────────┐
                    │  dim_date    │
                    │  ├─ date_key │
                    │  ├─ month    │
                    │  ├─ quarter  │
                    │  └─ year     │
                    └──────┬───────┘
                           │
┌──────────────┐    ┌──────▼────────────┐    ┌──────────────┐
│ dim_customer │────│  fact_orders      │────│ dim_store    │
│ ├─ customer_key│  │  ├─ order_id      │    │ ├─ store_id  │
│ ├─ name      │    │  ├─ customer_key  │    │ ├─ store_name│
│ ├─ segment   │    │  ├─ store_key     │    │ ├─ region    │
│ └─ tier      │    │  ├─ product_key   │    │ └─ city      │
└──────────────┘    │  ├─ amount        │    └──────────────┘
                    │  ├─ order_status  │
┌──────────────┐    │  └─ order_date_key│
│ dim_product  │────└───────────────────┘
│ ├─ product_id│
│ ├─ category  │
│ └─ type      │
└──────────────┘
```

### Áp dụng

| Layer | Vai trò trong BI |
|-------|-----------------|
| `staging` (stg_) | Raw data cleaned — không dùng trực tiếp cho BI |
| `golden` (cst_, ev_) | Business logic applied — source cho dimensions |
| `curated` (cur_) | **BI-ready** — star schema, aggregates, KPI tables |

### Deliverables

- [ ] Thiết kế star schema cho 1 business domain (ví dụ: E-commerce Orders, SaaS Subscriptions, hoặc Fintech Lending)
- [ ] Implement SCD Type 2 cho ít nhất 1 dimension
- [ ] Tạo 2+ aggregate tables cho dashboard performance
- [ ] Document data model (ERD diagram + column descriptions)
- [ ] Review & optimize existing curated models

---

## Phase 3: Data Visualization Best Practices (Tuần 11-14)

**Mục tiêu:** Thiết kế dashboards effective — không chỉ đẹp mà actionable.

### Nguyên tắc thiết kế

| Nguyên tắc | Mô tả | Anti-pattern |
|------------|--------|-------------|
| **1 dashboard = 1 question** | Mỗi dashboard trả lời 1 business question chính | Dashboard "tổng hợp mọi thứ" |
| **5-second rule** | User phải hiểu key insight trong 5 giây | Quá nhiều charts, không focus |
| **Data-ink ratio** | Maximize data, minimize decoration | 3D charts, gradient backgrounds |
| **Progressive disclosure** | Overview → Detail (drill-down) | Dump tất cả data lên 1 page |
| **Consistent encoding** | Cùng color = cùng meaning across dashboards | Xanh = tốt ở tab A, xấu ở tab B |
| **Mobile-friendly** | Responsive layout | Fixed-width dashboards |

### Dashboard Structure Template

```
┌─────────────────────────────────────────────────────┐
│  TITLE: [Business Question]       Filters: [▼]      │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐                 │
│  │ KPI │  │ KPI │  │ KPI │  │ KPI │  ← Big Numbers  │
│  │  1  │  │  2  │  │  3  │  │  4  │                 │
│  └─────┘  └─────┘  └─────┘  └─────┘                 │
│                                                     │
│  ┌─────────────────────┐  ┌──────────────────┐      │
│  │                     │  │                  │      │
│  │   Primary Chart     │  │  Secondary Chart │      │
│  │   (trend/compare)   │  │  (breakdown)     │      │
│  │                     │  │                  │      │
│  └─────────────────────┘  └──────────────────┘      │
│                                                     │
│  ┌─────────────────────────────────────────────┐    │
│  │             Detail Table (drill-down)       │    │
│  └─────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
```

### Color Palette Standards

| Ý nghĩa | Color | Hex |
|----------|-------|-----|
| Positive / Good | Green | #2ECC71 |
| Negative / Bad | Red | #E74C3C |
| Neutral / Info | Blue | #3498DB |
| Warning | Orange | #F39C12 |
| Company brand | (theo brand guide) | — |

### Deliverables

- [ ] Redesign 1 dashboard hiện có theo best practices
- [ ] Tạo dashboard style guide cho team (colors, fonts, layout)
- [ ] A/B test: 2 versions cùng data, hỏi user prefer cái nào
- [ ] Presentation: demo dashboard + explain design decisions

---

## Phase 4: KPI & Semantic Layer (Tuần 15-18)

**Mục tiêu:** Định nghĩa KPIs rõ ràng, tạo semantic layer để mọi người dùng chung definitions.

### Tại sao cần Semantic Layer?

| Vấn đề | Không có semantic layer | Có semantic layer |
|--------|------------------------|-------------------|
| Revenue = gì? | Mỗi analyst define khác | 1 definition duy nhất |
| Active customer = gì? | Query khác nhau | 1 metric, reusable |
| Ai đúng? | Tranh cãi, mất thời gian | Single source of truth |
| Thay đổi logic | Sửa 20 dashboards | Sửa 1 chỗ, cascade |

### KPI Documentation Template

```markdown
## KPI: [Tên KPI]

**Owner:** [Phòng ban / người chịu trách nhiệm]
**Frequency:** Daily / Weekly / Monthly
**Target:** [Con số mục tiêu]

### Definition
[Mô tả business chính xác]

### Formula
[Công thức tính — SQL hoặc text]

### Data Source
- Table: [curated table name]
- Column: [column name]
- Filter: [conditions]

### Caveats
- [Những trường hợp đặc biệt]
- [Exclusions]
- [Known limitations]

### History
| Date | Change | By |
|------|--------|----|
```

### KPIs theo Business Domains phổ biến

Dưới đây là một số KPIs cốt lõi của các mô hình kinh doanh chính mà một BI Engineer cần nắm vững để thiết kế dashboards phù hợp:

#### 1. E-commerce & Retail (Thương mại điện tử & Bán lẻ)
| Domain Area | KPI | Công thức / Định nghĩa | Ý nghĩa |
|-------------|-----|-------------------------|---------|
| Sales | GMV | `SUM(order_amount)` | Tổng giá trị giao dịch trong kỳ (chưa trừ hủy/hoàn) |
| Sales | AOV (Average Order Value)| `GMV / Total Orders` | Giá trị trung bình của mỗi đơn hàng |
| Conversion | CR (Conversion Rate) | `Purchasing Users / Total Visitors` | Tỷ lệ chuyển đổi khách ghé thăm thành người mua hàng |
| Customer | Cohort Retention Rate | `Active Cohort Users in Month N / Initial Cohort Users` | Tỷ lệ giữ chân khách hàng qua từng tháng |

#### 2. SaaS & Subscription (Phần mềm dịch vụ)
| Domain Area | KPI | Công thức / Định nghĩa | Ý nghĩa |
|-------------|-----|-------------------------|---------|
| Revenue | MRR / ARR | `SUM(active_subscription_value)` | Doanh thu định kỳ hàng tháng / hàng năm |
| Growth | CAC (Customer Acquisition Cost)| `Total Marketing Spend / New Customers` | Chi phí để có được 1 khách hàng mới |
| Unit Econ | LTV / CAC Ratio | `LTV / CAC` | Đo lường hiệu quả kinh tế trên mỗi khách hàng (đẹp nhất > 3) |
| Retention | Logo Churn Rate | `Cancelled Accounts / Active Accounts at Start` | Tỷ lệ tài khoản dừng/hủy gói dịch vụ |

#### 3. Banking & Fintech (Ngân hàng & Công nghệ tài chính)
| Domain Area | KPI | Công thức / Định nghĩa | Ý nghĩa |
|-------------|-----|-------------------------|---------|
| Credit | NPL Ratio (Tỷ lệ nợ xấu)| `Nợ xấu (nhóm 3-5) / Tổng dư nợ` | Đo lường chất lượng danh mục và rủi ro tín dụng |
| Credit | Disbursement Volume | `SUM(disbursed_amount)` | Tổng quy mô giải ngân trong kỳ |
| Customer | CASA Ratio | `(CASA Balance / Total Deposits) * 100` | Tỷ lệ tiền gửi không kỳ hạn (nguồn vốn rẻ) |
| Finance | NIM (Net Interest Margin)| `(Interest Income - Interest Expense) / Avg Earning Assets` | Biên thu nhập lãi thuần |
| Finance | CIR (Cost to Income Ratio)| `Operating Expense / Operating Income` | Hiệu quả vận hành (càng thấp càng tốt) |

### Deliverables

- [ ] KPI catalog document (20+ KPIs across 3 domains hoặc sâu vào domain chính của công ty)
- [ ] Implement metrics/semantic layer trong BI tool hoặc dbt (metrics / curated models)
- [ ] Mỗi KPI có: definition, formula, owner, source, caveats
- [ ] Training session cho business: "cách đọc dashboard & phân tích số liệu"
- [ ] Feedback loop: business validate KPI definitions

---

## Phase 5: Performance & Self-Service (Tuần 19-22)

**Mục tiêu:** Dashboards nhanh, users tự phục vụ được mà không cần request mỗi lần.

### Performance Optimization

| Technique | Khi nào dùng | Impact |
|-----------|-------------|--------|
| Materialized views | Queries phức tạp, join nhiều bảng | ⭐⭐⭐⭐⭐ |
| Aggregate tables | Dashboard cần pre-computed | ⭐⭐⭐⭐⭐ |
| Query caching | Data không đổi trong ngày | ⭐⭐⭐⭐ |
| Partition pruning | Filter theo date | ⭐⭐⭐⭐ |
| Dist/sort keys | Redshift-specific optimization | ⭐⭐⭐⭐ |
| Limit columns | SELECT chỉ cần thiết | ⭐⭐⭐ |
| Incremental refresh | Chỉ load data mới | ⭐⭐⭐⭐⭐ |

### Self-Service Strategy

```
Level 1: View-only dashboards (management)
         → Pre-built, click filters, no SQL needed

Level 2: Explore mode (power users)
         → Drill-down, custom filters, export data

Level 3: SQL access (analysts)
         → Curated tables, documented, governed

Level 4: Build dashboards (BI team)
         → Full access, create & publish
```

### Deliverables

- [ ] All production dashboards < 5s load time
- [ ] Self-service documentation cho business users
- [ ] Governance process: ai tạo/publish dashboard, review flow
- [ ] Usage analytics: track dashboard adoption (views, users, frequency)
- [ ] Optimize 3+ slow dashboards (document before/after metrics)

---

## Phase 6: BI Governance & Ops (Tuần 23+)

**Mục tiêu:** Vận hành BI như 1 product — versioning, monitoring, retirement.

### BI Governance Framework

| Aspect | Policy |
|--------|--------|
| **Creation** | MR review trước publish. Data source phải từ curated layer |
| **Naming** | `[Domain] - [Subject] - [Audience]` (VD: "Sales - Monthly Revenue - Executive Team") |
| **Ownership** | Mỗi dashboard có owner + backup owner |
| **Review cadence** | Quarterly review: còn dùng? data đúng? performance OK? |
| **Retirement** | > 30 ngày không ai xem → notify owner → archive sau 2 tuần |
| **Access** | Role-based: viewer, editor, admin per workspace |

### Dashboard Lifecycle

```
Draft → Review → Published → Active → Deprecated → Archived
  │       │          │          │          │
  └──fix──┘    ┌─────┘    ┌────┘     ┌────┘
               │          │          │
           Monitored   Quarterly   No views
           (alerts)    review      30+ days
```

### Deliverables

- [ ] BI governance document (creation, naming, review, retirement)
- [ ] Dashboard inventory (tất cả dashboards: owner, status, last viewed)
- [ ] Monitoring: alert khi data delay > SLA ảnh hưởng dashboard
- [ ] Retire 5+ unused dashboards (clean up)
- [ ] Onboarding guide cho BI consumer mới

---

## Skill Matrix — BI Engineer Levels

| Skill | Junior BI | Mid BI | Senior BI |
|-------|:---------:|:------:|:---------:|
| BI tool mastery | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Dimensional modeling | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Visualization design | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| SQL | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| KPI definitions | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Stakeholder comm | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Performance tuning | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Self-service enablement | — | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| BI governance | — | ⭐⭐ | ⭐⭐⭐⭐ |
| Semantic layer | — | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Mentoring | — | ⭐⭐ | ⭐⭐⭐⭐ |

---

## Timeline tổng quan

```
Tuần 1-4:    BI Tool Mastery (Superset + QuickSight)
Tuần 5-10:   Dimensional Modeling (star schema, SCD)
Tuần 11-14:  Visualization Best Practices
Tuần 15-18:  KPI & Semantic Layer
Tuần 19-22:  Performance & Self-Service
Tuần 23+:    BI Governance & Ops

→ 5 tháng để trở thành Mid BI Engineer
→ 12+ tháng để Senior BI (own semantic layer, governance)
```

---

## Đánh giá tiến độ

| Milestone | Criteria | Timeframe |
|-----------|----------|-----------|
| BI Starter | 3 dashboards live, basic filters | Tuần 4 |
| BI Competent | Star schema design, chart best practices | Tuần 10 |
| BI Independent | KPI catalog, semantic layer, self-service docs | Tuần 18 |
| BI Senior | Governance, performance, mentor team | Tuần 23+ |

---

## Career Path từ BI Engineer

```
                    ┌─────────────────┐
                    │ Senior BI Eng   │
                    └────────┬────────┘
                             │
              ┌──────────────┼────────────────────┐
              │              │                    │
     ┌────────▼────────┐ ┌───▼─────────┐ ┌────────▼──────────────┐
     │  Analytics Eng  │ │  BI Lead    │ │  Data Product Mgr     │
     │  (dbt pipeline) │ │  (team)     │ │  (strategy)           │
     └─────────────────┘ └─────────────┘ └───────────────────────┘
```

---

## Resources

### BI Tools
- [Apache Superset docs](https://superset.apache.org/docs/intro)
- [Amazon QuickSight User Guide](https://docs.aws.amazon.com/quicksight/)
- [Metabase docs](https://www.metabase.com/docs/latest/)

### Data Visualization
- [Storytelling with Data (Cole Nussbaumer)](http://www.storytellingwithdata.com/) — must-read
- [The Big Book of Dashboards](https://www.bigbookofdashboards.com/)
- [Information is Beautiful](https://informationisbeautiful.net/) — inspiration
- [Datawrapper Blog](https://blog.datawrapper.de/) — practical chart advice
- [Nightingale (Data Viz Society)](https://nightingaledvs.com/) — articles

### Dimensional Modeling
- [The Data Warehouse Toolkit (Kimball)](https://www.kimballgroup.com/) — bible
- [Star Schema: The Complete Reference](https://www.amazon.com/Star-Schema-Complete-Reference/dp/0071744320)
- [Slowly Changing Dimensions explained](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-1-2-3/)
- [dbt Dimensional Modeling](https://docs.getdbt.com/terms/dimensional-modeling)

### KPI & Metrics
- [Lean Analytics (Alistair Croll)](http://leananalyticsbook.com/) — metric selection
- [Measure What Matters (John Doerr)](https://www.whatmatters.com/) — OKR framework
- [Data Council talks on Metrics Layer](https://www.datacouncil.ai/)

### Performance
- [Redshift Query Performance](https://docs.aws.amazon.com/redshift/latest/dg/c-optimizing-query-performance.html)
- [Superset Performance Tuning](https://superset.apache.org/docs/installation/cache/)
- [QuickSight SPICE optimization](https://docs.aws.amazon.com/quicksight/latest/user/spice.html)

### BI Strategy & Governance
- [Gartner BI Maturity Model](https://www.gartner.com/)
- [Self-Service BI Guide (Tableau)](https://www.tableau.com/learn/whitepapers/self-service-analytics)
- [BI Governance Framework (TDWI)](https://tdwi.org/)
