# Career Roadmap: Data Analyst

## Bối cảnh

Roadmap dành cho thành viên mới hoặc đang ở vị trí Data Analyst . Mục tiêu: thành thạo phân tích dữ liệu, SQL nâng cao, business domain, và trở thành người có thể tự giải quyết mọi yêu cầu ad-hoc từ business.

---

## Vai trò Data Analyst 

| Aspect | Mô tả |
|--------|--------|
| **Mission** | Biến dữ liệu thô thành insight actionable cho business |
| **Collaborate with** | Business teams (Sales, Marketing, Finance, Operations...), BI Engineer, AE |
| **Output** | Reports, ad-hoc analysis, data exploration, business requirements |
| **Tools** | SQL (Redshift), Excel, Python (pandas), DBeaver |

---

## Phase 1: SQL Foundation (Tuần 1-4)

**Mục tiêu:** Viết được queries phức tạp, hiểu execution plan.

### Skills

| Skill | Level cần đạt | Ghi chú |
|-------|:-------------:|---------|
| SELECT, JOIN, GROUP BY | ⭐⭐⭐⭐⭐ | Phải master hoàn toàn |
| Subqueries & CTEs | ⭐⭐⭐⭐ | CTE > subquery (readability) |
| Window Functions | ⭐⭐⭐⭐ | ROW_NUMBER, RANK, LAG, LEAD, SUM OVER |
| CASE WHEN logic | ⭐⭐⭐⭐ | Business logic phức tạp |
| Date functions | ⭐⭐⭐⭐ | DATEDIFF, DATE_TRUNC, EXTRACT |
| String functions | ⭐⭐⭐ | SUBSTRING, REGEXP, SPLIT_PART |
| NULL handling | ⭐⭐⭐⭐ | COALESCE, NULLIF, IS DISTINCT FROM |

### Bài tập thực hành

| # | Bài tập | Domain |
|---|---------|--------|
| 1 | Tính số dư hoặc doanh thu trung bình 30 ngày gần nhất per khách hàng | Finance / Sales |
| 2 | Rank chi nhánh hoặc dòng sản phẩm theo doanh số tháng (window function) | Sales / Retail |
| 3 | Tìm khách hàng có giao dịch hoặc hành vi bất thường (> 3 std dev) | Risk / Fraud |
| 4 | Pivot report: doanh thu theo tháng × danh mục sản phẩm | E-commerce / Sales |
| 5 | Tính retention rate hoặc month-over-month growth | Finance / Growth / SaaS |

### Deliverables

- [ ] Hoàn thành 20+ complex queries trên Redshift
- [ ] Giải thích được EXPLAIN plan cho 1 slow query
- [ ] Viết CTE chain 3+ levels cho business logic phức tạp
- [ ] Sử dụng window functions thành thạo (partition by + order by)

---

## Phase 2: Data Exploration & Profiling (Tuần 5-8)

**Mục tiêu:** Tự explore data source mới, hiểu schema, phát hiện anomaly.

### Skills

| Skill | Level cần đạt | Ghi chú |
|-------|:-------------:|---------|
| Schema exploration | ⭐⭐⭐⭐ | information_schema, pg_table_def |
| Data profiling | ⭐⭐⭐⭐ | NULL %, distinct count, min/max, distribution |
| Source understanding | ⭐⭐⭐⭐ | Biết data đến từ đâu (T24, EOZ, external) |
| Data quality check | ⭐⭐⭐ | Phát hiện duplicates, missing, outliers |
| Documentation | ⭐⭐⭐ | Ghi lại findings, business logic |

### Profiling Template

```sql
-- Template profiling cho bất kỳ bảng nào
SELECT
    COUNT(*) AS total_rows,
    COUNT(DISTINCT customer_id) AS unique_customers,
    MIN(created_date) AS earliest_date,
    MAX(created_date) AS latest_date,
    SUM(CASE WHEN phone IS NULL THEN 1 ELSE 0 END)::FLOAT / COUNT(*) AS null_phone_pct,
    COUNT(DISTINCT status) AS status_values
FROM schema.table_name
WHERE tf_partition_date = '{{ business_date }}';
```

### Deliverables

- [ ] Profile 5 source tables từ các hệ thống nghiệp vụ (CRM, ERP, E-commerce DB, Core DB...), document findings
- [ ] Tạo data dictionary cho 1 domain (columns, types, business meaning)
- [ ] Identify data quality issues và report cho team
- [ ] Hiểu data flow: source → staging → golden → curated

---

## Phase 3: Business Domain Knowledge (Tuần 9-12)

**Mục tiêu:** Hiểu nghiệp vụ đủ sâu để tự dịch yêu cầu business → SQL.

### Enterprise Domains

Một Data Analyst cần hiểu rõ dữ liệu của domain mình đang phân tích. Dưới đây là các thực thể chính của 3 domains phổ biến:

#### 1. E-commerce / Retail
| Domain Area | Entities chính | Bảng quan trọng ví dụ |
|-------------|---------------|-----------------------|
| **Sales & Orders** | Đơn hàng, sản phẩm, giỏ hàng, thanh toán | `stg_orders`, `stg_order_items` |
| **Customer** | Khách hàng, phân khúc, địa chỉ, lịch sử mua | `dim_users`, `dim_customers` |
| **Inventory** | Kho hàng, tồn kho, nhà cung cấp, vận chuyển | `stg_inventory`, `stg_shipping` |
| **Marketing** | Chiến dịch, click, coupon, chi phí quảng cáo | `stg_campaigns`, `stg_ad_spend` |

#### 2. SaaS / Subscription
| Domain Area | Entities chính | Bảng quan trọng ví dụ |
|-------------|---------------|-----------------------|
| **Subscription** | Gói dịch vụ, chu kỳ, trạng thái (active/churn) | `stg_subscriptions`, `stg_invoices` |
| **Product Usage** | Session, event, tính năng sử dụng, thời lượng | `stg_user_events`, `stg_sessions` |
| **Customer Success**| Ticket hỗ trợ, điểm CSAT/NPS, phản hồi | `stg_support_tickets`, `stg_nps` |

#### 3. Banking & Fintech
| Domain Area | Entities chính | Bảng quan trọng ví dụ |
|-------------|---------------|-----------------------|
| **Credit (Tín dụng)**| Khoản vay, hạn mức, lãi suất, nợ xấu | `stg_loans`, `stg_credit_limits` |
| **Customer** | CIF (Customer Info File), KYC, segment | `dim_customers`, `dim_customer_profiles`|
| **Transaction** | Giao dịch, chuyển khoản, thanh toán, ATM/POS | `stg_transactions`, `stg_trans_logs` |
| **Finance** | Tài khoản, số dư, bảng cân đối, P&L | `stg_accounts`, `stg_gl_balances` |

### Thuật ngữ cần biết (Business Glossary)

| Lĩnh vực | Thuật ngữ | Ý nghĩa | Ví dụ |
|----------|-----------|----------|-------|
| **Chung / SaaS** | **LTV** | Lifetime Value — Giá trị vòng đời khách hàng | LTV = 150 USD |
| | **CAC** | Customer Acquisition Cost — Chi phí thu hút KH | CAC = 30 USD |
| | **Churn Rate** | Tỷ lệ khách hàng dừng dịch vụ / rời bỏ | Churn Rate = 5% / tháng |
| **E-commerce** | **AOV** | Average Order Value — Giá trị đơn hàng trung bình | AOV = 250,000 VND |
| | **GMV** | Gross Merchandise Value — Tổng giá trị giao dịch | GMV = 2 tỷ / tháng |
| | **CR (Conversion)**| Conversion Rate — Tỷ lệ chuyển đổi mua hàng | CR = 2.5% |
| **Banking / Fintech** | **CIF / KYC** | Customer Info File / Know Your Customer | Mã CIF001234 |
| | **NPL** | Non-Performing Loan — Tỷ lệ nợ xấu (nhóm 3-5) | NPL ratio = 1.8% |
| | **CASA** | Current Account Savings Account — Tiền gửi không kỳ hạn| CASA ratio = 25% |

### Deliverables

- [ ] Hiểu business flow cho 2 domains chính của doanh nghiệp
- [ ] Tự viết query cho 5 yêu cầu business thực tế (không cần hỏi lại)
- [ ] Tạo glossary 30+ thuật ngữ business đặc trưng của domain công ty đang làm việc
- [ ] Shadow 1 buổi meeting với business team, ghi lại requirements

---

## Phase 4: Python cho Analysis (Tuần 13-16)

**Mục tiêu:** Dùng Python khi SQL không đủ — statistical analysis, automation, visualization.

### Skills

| Skill | Library | Level cần đạt |
|-------|---------|:-------------:|
| DataFrames | pandas | ⭐⭐⭐⭐ |
| Data manipulation | pandas (merge, pivot, groupby) | ⭐⭐⭐⭐ |
| Visualization | matplotlib, seaborn | ⭐⭐⭐ |
| Statistics | scipy.stats, numpy | ⭐⭐⭐ |
| Excel automation | openpyxl, xlsxwriter | ⭐⭐⭐ |
| DB connection | psycopg2, sqlalchemy | ⭐⭐⭐ |

### Khi nào dùng Python thay SQL?

| Situation | Dùng |
|-----------|------|
| Query data, join, aggregate | SQL ✅ |
| Statistical testing (t-test, chi-square) | Python ✅ |
| Complex visualization | Python ✅ |
| Automate repetitive reports | Python ✅ |
| Export nhiều format (Excel, CSV, PDF) | Python ✅ |
| Data > 1M rows, simple transform | SQL ✅ |

### Deliverables

- [ ] Script kết nối Redshift, query → pandas DataFrame
- [ ] 2 analysis notebooks (exploratory + statistical)
- [ ] 1 automated report script (query → Excel → email/share)
- [ ] Visualization cho 1 business presentation

---

## Phase 5: Reporting & Communication (Tuần 17-20)

**Mục tiêu:** Trình bày findings rõ ràng, actionable cho non-technical audience.

### Skills

| Skill | Level cần đạt | Ghi chú |
|-------|:-------------:|---------|
| Storytelling with data | ⭐⭐⭐⭐ | Insight → So what? → Action |
| Excel reporting | ⭐⭐⭐⭐ | Pivot, conditional formatting, charts |
| Slide deck creation | ⭐⭐⭐ | 1 slide = 1 message |
| Requirements gathering | ⭐⭐⭐⭐ | Hỏi đúng câu hỏi trước khi query |
| Stakeholder management | ⭐⭐⭐ | Set expectation, timeline, scope |

### Framework trả lời yêu cầu business

```
1. CLARIFY: "Anh/chị muốn biết gì chính xác? Cho ai? Quyết định gì?"
2. SCOPE: "Timeframe? Segment? So sánh với gì?"
3. EXPLORE: Query, profile, validate data
4. ANALYZE: Tìm patterns, anomalies, insights
5. PRESENT: Charts + numbers + SO WHAT + recommendation
6. DOCUMENT: Ghi lại logic, assumptions, caveats
```

### Deliverables

- [ ] Deliver 5+ ad-hoc reports cho business teams
- [ ] 1 presentation deck với data storytelling
- [ ] Template báo cáo chuẩn (reusable cho team)
- [ ] Requirements document cho 1 recurring report

---

## Phase 6: Advanced & Specialization (Tuần 21-24)

**Mục tiêu:** Chuyên sâu 1 domain, tự chủ hoàn toàn, mentor người mới.

### Advanced Skills

| Skill | Level cần đạt | Ghi chú |
|-------|:-------------:|---------|
| Query optimization | ⭐⭐⭐⭐ | EXPLAIN, dist key, sort key |
| Cohort analysis | ⭐⭐⭐⭐ | Retention, churn, LTV |
| A/B testing basics | ⭐⭐⭐ | Sample size, significance |
| Forecasting basics | ⭐⭐⭐ | Moving average, trend decomposition |
| Data pipeline awareness | ⭐⭐⭐ | Hiểu dbt, Airflow ở high level |

### Deliverables

- [ ] Own 1 domain hoàn toàn (mọi yêu cầu → tự giải quyết)
- [ ] Tối ưu 3+ slow queries (giảm > 50% runtime)
- [ ] Cohort analysis cho 1 business question
- [ ] Mentor 1 junior DA mới vào team
- [ ] Propose 1 insight chủ động (không ai yêu cầu)

---

## Skill Matrix — Data Analyst Levels

| Skill | Junior DA | Mid DA | Senior DA |
|-------|:---------:|:------:|:---------:|
| SQL | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Python | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Business domain | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Data profiling | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Communication | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Statistics | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Excel/Sheets | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Pipeline awareness | — | ⭐⭐ | ⭐⭐⭐ |
| Mentoring | — | — | ⭐⭐⭐ |
| Self-initiative | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

## Timeline tổng quan

```
Tuần 1-4:    SQL Foundation (master queries)
Tuần 5-8:    Data Exploration & Profiling
Tuần 9-12:   Business Domain Knowledge
Tuần 13-16:  Python cho Analysis
Tuần 17-20:  Reporting & Communication
Tuần 21-24:  Advanced & Specialization

→ 6 tháng để trở thành Mid DA tự chủ
→ 12+ tháng để Senior DA (own domain, mentor)
```

---

## Đánh giá tiến độ

| Milestone | Criteria | Timeframe |
|-----------|----------|-----------|
| DA Starter | Basic SQL, explore data với hướng dẫn | Tuần 4 |
| DA Competent | Complex queries, self-explore, profile data | Tuần 8 |
| DA Independent | Business domain, tự dịch requirements → SQL | Tuần 12 |
| DA Productive | Python + reporting, deliver ad-hoc independently | Tuần 16-20 |
| DA Senior | Own domain, optimize, mentor, proactive insights | Tuần 24+ |

---

## Career Path từ DA

```
                    ┌─────────────────┐
                    │   Senior DA     │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
     ┌────────▼────────┐ ┌──▼───────┐ ┌───▼──────────┐
     │  BI Engineer    │ │  AE      │ │  Data Scientist│
     │  (visualization)│ │  (dbt)   │ │  (ML/stats)    │
     └─────────────────┘ └──────────┘ └───────────────┘
```

---

## Resources

### SQL
- [Mode Analytics SQL Tutorial](https://mode.com/sql-tutorial/) — window functions
- [SQLBolt](https://sqlbolt.com/) — interactive exercises
- [Use The Index, Luke](https://use-the-index-luke.com/) — performance
- [Redshift SQL Reference](https://docs.aws.amazon.com/redshift/latest/dg/cm_chap_SQLCommandRef.html)
- [Window Functions cheat sheet](https://learnsql.com/blog/sql-window-functions-cheat-sheet/)

### Python
- [Python for Data Analysis (Wes McKinney)](https://wesmckinney.com/book/) — pandas bible
- [Real Python](https://realpython.com/) — practical tutorials
- [Kaggle Learn](https://www.kaggle.com/learn) — free hands-on courses

### Business & Communication
- [Storytelling with Data (Cole Nussbaumer)](http://www.storytellingwithdata.com/)
- [The Pyramid Principle (Barbara Minto)](https://www.amazon.com/Pyramid-Principle-Logic-Writing-Thinking/) — structured thinking
- [Measure What Matters (John Doerr)](https://www.whatmatters.com/) — KPI thinking

### Domain Knowledge Resources
- [E-commerce & Retail Metrics (Klipfolio)](https://www.klipfolio.com/resources/kpi-examples/ecommerce)
- [SaaS Metrics 2.0 (David Skok)](https://www.forentrepreneurs.com/saas-metrics-2/)
- [Banking & Fintech 101 (Investopedia)](https://www.investopedia.com/banking-4427797)
- Company business glossary & internal wiki (Confluence)
