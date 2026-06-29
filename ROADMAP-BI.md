# 📈 Business Intelligence (BI Analyst/Developer) — Career Roadmap

> *Bạn có bao giờ nhìn một dashboard đẹp, tương tác mượt mà, và tự hỏi ai đã xây nó không? Đằng sau mỗi dashboard mà CEO mở mỗi sáng, đằng sau mỗi con số real-time mà team operations theo dõi liên tục — có một BI Developer đã dành hàng tuần design data model, tune performance, và chọn từng màu sắc cho phù hợp. Một dashboard tốt có thể thay đổi cách 500 người trong tổ chức ra quyết định mỗi sáng. BI không phải "người kéo thả biểu đồ" — mà là kiến trúc sư của hệ thống thông tin, người biến chaos data thành visual clarity cho cả tổ chức. Nếu DA tìm insight, thì BI xây cả con đường để mọi người tự tìm insight. BI là cầu nối giữa data và decisions — và bạn là người xây cầu đó.*

## 1. Tổng quan vai trò

**BI Analyst/Developer** thiết kế và xây dựng hệ thống reporting, dashboard, và data visualization giúp tổ chức theo dõi hiệu quả hoạt động kinh doanh. BI chuyên sâu hơn DA về mặt xây dựng hệ thống báo cáo quy mô lớn và data modeling cho visualization.

### Responsibilities chính
- Thiết kế & phát triển interactive dashboards và reports
- Xây dựng data model (star/snowflake schema) cho reporting
- Tạo KPI tracking systems cho management
- Tối ưu hóa performance cho large-scale dashboards
- Maintain data catalog và documentation
- Training business users tự kéo report
- Collaborate với DE để define data requirements

### Phân biệt BI Analyst vs DA

| Tiêu chí | Data Analyst | BI Analyst/Developer |
|---|---|---|
| Focus | Ad-hoc analysis, deep-dive | Scalable reporting systems |
| Output | Insight reports, recommendations | Dashboards, data products |
| Users | Specific stakeholders | Toàn tổ chức |
| Tools trọng tâm | SQL + Python + Stats | BI Platform + Data Modeling |
| Tần suất | On-demand analysis | Recurring, automated |

---

## 2. Skill Matrix theo Level

> BI có một nghịch lý thú vị: tool thì dễ học (Power BI cơ bản chỉ cần 2 tuần), nhưng làm giỏi thì cần hiểu sâu data modeling, performance, và UX. Người fresher kéo biểu đồ, người senior thiết kế hệ thống cho 500 người dùng đồng thời mà dashboard vẫn load trong 3 giây. Khoảng cách đó không phải tool — mà là tư duy.

### Technical Skills

| Skill | Intern | Fresher | Junior | Mid | Senior |
|---|:---:|:---:|:---:|:---:|:---:|
| BI Platform (Power BI/Tableau/Looker) | Explore reports | Basic dashboard | Multi-page, filters | Composite model, RLS | Governance, deployment |
| SQL | SELECT, JOIN | Aggregation, subquery | Window fn, CTEs | Optimization, profiling | Architecture awareness |
| Data Modeling | - | Star schema concept | Build dim/fact tables | Complex models, SCD | Enterprise model design |
| DAX / Calculated Fields | - | Basic measures | Time intelligence | Advanced patterns | Optimization |
| ETL/Data Pipeline | - | Understand flow | Power Query / Dataflows | Design pipelines | Orchestration strategy |
| Semantic Layer | - | - | Understand measures | Design metrics layer | Enterprise semantic layer |
| Performance Tuning | - | - | Query folding | Incremental refresh | At-scale optimization |
| Version Control | - | - | Basic Git | Branching, CI/CD | DevOps for BI |
| Cloud (AWS/Azure/GCP) | - | - | Basic concepts | Specific services | Multi-cloud |

### Soft Skills

| Skill | Intern | Fresher | Junior | Mid | Senior |
|---|:---:|:---:|:---:|:---:|:---:|
| Requirements Gathering | - | Listen & note | Ask clarifying Qs | Lead discovery sessions | Define strategy |
| UX/Design Thinking | - | Basic layout | User-centered design | Usability testing | Design system |
| Documentation | Note-taking | Basic guides | User guides | Standards & SOPs | Governance docs |
| Training | - | - | 1:1 support | Workshop delivery | Training program design |
| Stakeholder Mgmt | - | Respond | Proactive communication | Manage priorities | Influence roadmap |

---

## 3. Learning Path chi tiết

> Học BI giống như học nấu ăn: bắt đầu từ trứng chiên (basic dashboard), rồi lên phở (multi-source model), rồi đến fine dining (enterprise platform). Mỗi phase đều cần thời gian ngấm — đừng vội skip phase 1 chỉ vì "thấy dễ." Người nấu trứng chiên ngon mới nấu được phở ngon.

### Phase 1: Foundation (Intern → Fresher) — 0-6 tháng

**Mục tiêu:** Xây được dashboard cơ bản, hiểu data flow

| Tuần | Chủ đề | Output |
|---|---|---|
| 1-3 | SQL fundamentals: queries, joins, aggregation | 15+ practice queries |
| 4-6 | Power BI/Tableau basics: connect, transform, visualize | 2 basic dashboards |
| 7-9 | Data modeling intro: star schema, relationships | 1 model diagram |
| 10-12 | Chart selection & design principles | Dashboard redesign exercise |
| 13-16 | DAX/Calculated fields basics | Measures library |
| 17-20 | Power Query / ETL basics | Data prep workflow |
| 21-24 | Mini project: company metrics dashboard | Portfolio piece #1 |

### Phase 2: Core Competency (Fresher → Junior) — 6-12 tháng

**Mục tiêu:** Tự xây dashboard end-to-end cho business use case

| Tuần | Chủ đề | Output |
|---|---|---|
| 1-4 | Advanced data modeling: SCD, bridge tables | Complex model |
| 5-8 | DAX deep dive: time intelligence, filter context | Pattern library |
| 9-12 | Performance optimization: query folding, aggregations | Optimization checklist |
| 13-16 | Row-Level Security (RLS) & data governance | Security model |
| 17-20 | Storytelling with dashboards: layout, UX | Dashboard style guide |
| 21-24 | Project: multi-department reporting solution | Portfolio piece #2 |

### Phase 3: Advanced (Junior → Mid) — 12-24 tháng

**Mục tiêu:** Architect reporting solutions, manage BI lifecycle

| Tháng | Chủ đề | Output |
|---|---|---|
| 1-2 | Enterprise data modeling: conformed dimensions | Enterprise model |
| 3-4 | Semantic layer design (Looker LookML / dbt metrics) | Metrics layer |
| 5-6 | CI/CD cho BI: Git, deployment pipelines | DevOps pipeline |
| 7-8 | Cloud warehousing: Redshift/BigQuery/Snowflake | Cloud architecture |
| 9-10 | Advanced viz: custom visuals, embedded analytics | Custom solutions |
| 11-12 | BI strategy & roadmap planning | Strategy document |

### Phase 4: Expert (Mid → Senior) — 24+ tháng

**Mục tiêu:** Define BI strategy, lead platform adoption

| Tháng | Chủ đề | Output |
|---|---|---|
| 1-3 | BI platform governance: workspace, access, monitoring | Governance framework |
| 4-6 | Self-service BI strategy: training, templates, standards | Self-service program |
| 7-9 | AI-augmented BI: NLQ, automated insights, copilot | AI integration plan |
| 10-12 | Enterprise architecture: data mesh, federated BI | Architecture blueprint |
| Ongoing | Leadership, hiring BI talent, vendor evaluation | Team & platform growth |

---

## 4. Resources

> Thế giới BI có SQLBI — thánh địa cho mọi Power BI developer. Marco Russo và Alberto Ferrari đã viết gần như mọi thứ bạn cần biết về DAX. Ngoài ra, cộng đồng Tableau Public là nơi bạn xem người khác visualize data và tự hỏi "sao họ làm đẹp vậy?" — đó là cách học nhanh nhất. Học BI không chỉ đọc docs — mà xem người khác build, rồi tự tay replicate.

### Courses (Verified ✓)

| Course | Platform | Level | Link |
|---|---|---|---|
| Microsoft Power BI Data Analyst (PL-300) | Microsoft Learn | Mid | [Link](https://learn.microsoft.com/en-us/certifications/power-bi-data-analyst-associate/) |
| Tableau Desktop Specialist | Tableau eLearning | Beginner | [Link](https://www.tableau.com/learn/certification/desktop-specialist) |
| Google Business Intelligence Certificate | Coursera | Beginner-Mid | [Link](https://www.coursera.org/professional-certificates/google-business-intelligence) |
| DAX Patterns | SQLBI | Mid-Senior | [Link](https://www.daxpatterns.com/) |
| Power BI Guided Learning | Microsoft | Beginner | [Link](https://learn.microsoft.com/en-us/power-bi/guided-learning/) |
| Looker & LookML | Google Cloud Skills Boost | Mid | [Link](https://www.cloudskillsboost.google/paths/28) |
| Data Modeling for Power BI | SQLBI | Mid | [Link](https://www.sqlbi.com/courses/) |

### Books

| Sách | Tác giả | Level |
|---|---|---|
| The Definitive Guide to DAX (2nd Ed) | Marco Russo & Alberto Ferrari | Mid-Senior |
| Star Schema: The Complete Reference | Christopher Adamson | Mid |
| Information Dashboard Design | Stephen Few | All |
| Storytelling with Data | Cole Nussbaumer Knaflic | All |
| The Data Warehouse Toolkit (3rd Ed) | Ralph Kimball | Mid-Senior |
| Fundamentals of Data Engineering | Joe Reis & Matt Housley | Mid |

### Certifications

| Cert | Tổ chức | Giá trị |
|---|---|---|
| Microsoft PL-300 (Power BI Data Analyst) | Microsoft | ⭐ Industry standard |
| Tableau Desktop Specialist | Salesforce | Visualization focus |
| Tableau Certified Data Analyst | Salesforce | Advanced |
| Google Business Intelligence Certificate | Google | Comprehensive |
| AWS Certified Data Analytics - Specialty | AWS | Cloud BI |
| Looker Developer Certificate | Google Cloud | Modern BI |

### Communities & Practice

| Resource | Link |
|---|---|
| SQLBI (Power BI best practices) | [sqlbi.com](https://www.sqlbi.com/) |
| Power BI Community | [community.powerbi.com](https://community.powerbi.com/) |
| Tableau Public Gallery | [public.tableau.com](https://public.tableau.com/) |
| Workout Wednesday (Tableau) | [workout-wednesday.com](https://workout-wednesday.com/) |
| MakeoverMonday | [makeovermonday.co.uk](https://makeovermonday.co.uk/) |
| r/PowerBI | [reddit.com/r/PowerBI](https://www.reddit.com/r/PowerBI/) |

---

## 5. Portfolio Projects gợi ý

> Portfolio BI là visual — hãy cho người ta "thấy" trước khi đọc. Publish dashboard lên Power BI Service hoặc Tableau Public, kèm write-up giải thích data model phía sau. Một dashboard đẹp + performant + có RLS = 90% cơ hội pass technical round. Nhà tuyển dụng BI muốn "xem" — không chỉ "đọc."

### Intern/Fresher Level
1. **Sales Performance Dashboard** — Doanh thu, GP, top products (Power BI)
2. **HR Headcount Report** — Employee demographics, turnover (Tableau)
3. **Financial Snapshot** — P&L, balance sheet visualization (Excel → Power BI migration)

### Junior Level
4. **Multi-source Executive Dashboard** — Kết hợp CRM + ERP + GA data
5. **Customer 360 Report** — Profile, transactions, segments, lifetime value
6. **Operational Monitoring** — Real-time metrics, alerts, SLA tracking

### Mid Level
7. **Self-service Analytics Platform** — Template dashboards, governed datasets, training
8. **Embedded Analytics** — Dashboard trong product/app (Power BI Embedded / Tableau Embedded)
9. **Data Model Migration** — Legacy reports → modern semantic model

### Senior Level
10. **Enterprise BI Governance** — Standards, lifecycle, access control, audit
11. **AI-Powered Insights** — Natural language queries, anomaly detection
12. **BI Center of Excellence** — Framework, team, processes, tools selection

---

## 6. Interview Prep

> Interview BI thường có 2 phần: technical (DAX, data model) và case study (thiết kế dashboard cho scenario X). Tip: luôn hỏi ngược lại "Ai là end-user? Họ xem report bao lâu 1 lần? Trên mobile hay desktop?" — đó là tư duy BI thực sự. Người pass không phải người code DAX nhanh nhất — mà là người hiểu user nhất.

### Technical Questions (by level)

**Fresher/Junior:**
- Star schema vs snowflake schema — when to use which?
- Giải thích filter context trong DAX
- Khi nào dùng Import mode vs DirectQuery?
- Thiết kế dashboard cho CFO muốn track revenue — bạn hỏi gì?

**Mid:**
- Performance: dashboard load chậm 30s, troubleshoot thế nào?
- Design RLS cho org 500 người, 5 departments, cross-department managers
- Composite model: khi nào combine Import + DirectQuery?
- Incremental refresh strategy cho bảng 100M rows

**Senior:**
- BI platform migration: Tableau → Power BI, plan migration cho 200 users
- Self-service vs centralized BI — khi nào áp dụng mô hình nào?
- Data mesh impact lên BI architecture
- AI/ML integration trong BI roadmap: realistic vs hype

### Case Study Format
1. **Understand** — Requirements, users, frequency, data sources
2. **Design** — Data model, visualizations, interactions
3. **Implement** — Tool selection, performance considerations
4. **Govern** — Security, refresh, monitoring
5. **Scale** — Self-service, adoption, training

---

## 7. Salary Benchmark (VN Market 2025-2026)

> BI tại VN banking sector đang "nóng." Power BI là de facto standard, PL-300 cert gần như bắt buộc cho mid+. Good news: salary BI competitive với DA, và nếu bạn có thêm dbt/engineering skills thì nhảy thẳng sang BI Engineer range. Đầu tư 3 tháng lấy PL-300 = ROI cực cao cho career.

| Level | YoE | Salary Range (VND/tháng) | Note |
|---|---|---|---|
| Intern | 0 | 3-7 triệu | Part-time, practice tools |
| Fresher | 0-1 | 8-15 triệu | Basic dashboarding |
| Junior | 1-2 | 13-25 triệu | Independent delivery |
| Mid | 2-4 | 22-45 triệu | Complex solutions |
| Senior | 4-7+ | 40-75 triệu | Platform strategy |
| Lead/Manager | 7+ | 55-110+ triệu | Team + Architecture |

> *Power BI demand cao nhất tại VN (banking, enterprise). Tableau phổ biến ở MNCs. Looker/Metabase growing trong startups.*

---

## 8. Career Progression Paths từ BI

> BI là ngã tư có nhiều lối rẽ hấp dẫn. Nếu thích kỹ thuật sâu hơn → BI Engineer. Nếu thích manage → Head of BI. Nếu thích architecture → Solutions Architect. Đừng nghĩ BI chỉ là "kéo dashboard" — đó là bước đầu, không phải đích đến.

```
BI Senior ─┬─→ BI Manager → Head of BI/Analytics
           ├─→ BI Engineer (technical depth, xem ROADMAP-BI-ENGINEER.md)
           ├─→ Data Engineer
           ├─→ Analytics Engineer
           └─→ Solutions Architect (BI/Data Platform)
```


---

## 9. Day-in-the-Life Stories

> "BI Developer làm gì cả ngày?" — câu hỏi mà nhiều người chưa bắt đầu thường thắc mắc. Spoiler: không phải chỉ kéo thả chart. Level càng cao, bạn càng ít build dashboard mới và càng nhiều thời gian govern, optimize, train người khác. Nhưng cảm giác khi dashboard bạn build được 500 người dùng mỗi ngày — rất đáng.

### Fresher BI — Retail

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 9:00 | Check email, đọc requirements từ business | Outlook |
| 9:30 | Import data từ Excel vào Power BI | Power BI Desktop |
| 10:00 | Transform: rename columns, handle nulls | Power Query |
| 10:30 | Tạo relationships giữa tables | Power BI Model view |
| 11:00 | Build basic measures: Total Revenue, Count Orders | DAX |
| 11:30 | Hỏi senior: "Slicer filter không work cross-page?" | Teams |
| 13:00 | Design dashboard layout: cards, bar chart, table | Power BI |
| 14:30 | Senior review: "Colors không consistent, legend redundant" | - |
| 15:00 | Fix UX issues, apply company color theme | Power BI |
| 16:00 | Publish to workspace, set refresh schedule | Power BI Service |
| 16:30 | Document: data sources, refresh time | Confluence |

> **Cảm nhận:** "Chủ yếu học tool, cách connect data, build simple visuals. Senior guide nhiều về UX."

### Mid BI — Banking

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:30 | Check refresh failures, fix broken dataflow | Power BI Service |
| 9:00 | Requirements gathering meeting với CFO team | Teams |
| 10:00 | Design data model: 5 fact tables, 8 dimensions | dbdiagram.io, Power BI |
| 11:00 | Implement RLS: mỗi branch chỉ thấy data của mình | DAX |
| 13:00 | Performance tuning: report load 25s → target 5s | DAX Studio, Perf Analyzer |
| 14:30 | Build composite model: Import + DirectQuery | Power BI |
| 15:30 | Deploy to UAT, share for feedback | Power BI Service |
| 16:00 | Create documentation: user guide + technical spec | Confluence |
| 16:30 | Review junior's dashboard | Teams |

> **Cảm nhận:** "Focus vào architecture + performance. Balance between business needs và technical constraints."

### Senior BI — Enterprise (500+ users)

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:00 | Monitor: workspace usage, refresh success rate | Power BI Admin Portal |
| 8:30 | Incident: finance dashboard broken after source change | Power BI, Slack |
| 9:30 | Strategy meeting: BI roadmap Q3, new data sources | Teams, Miro |
| 10:30 | Design self-service framework: templates, governed datasets | Power BI, Confluence |
| 11:30 | Vendor demo: evaluate embedded analytics solution | Teams |
| 13:00 | Workshop: train 20 business analysts on self-service | Power BI, Teams |
| 14:30 | Architecture: data mesh impact on BI workspace structure | Miro |
| 15:30 | 1:1 with junior: career development, skill gaps | Teams |
| 16:30 | Write: "BI Governance Standards v2.0" | Confluence |

> **Cảm nhận:** "Less building, more governing. Define how 500 users use BI platform effectively and safely."

---

## 10. Vietnam Job Market Analysis

> Power BI đang "thống trị" VN banking sector. Nếu bạn target banking/enterprise, PL-300 gần như là vé vào cửa. Nếu bạn thích startup/fintech, Looker hoặc Metabase + dbt sẽ relevant hơn. Chọn tool theo target market, không theo trend.

### Top Companies tuyển BI tại VN

| Tier | Companies | Stack | 
|---|---|---|
| Banking | VPBank, Techcombank, MBBank, VIB, ACB | Power BI + Redshift/Oracle |
| Fintech | MoMo, VNPay, ZaloPay | Looker/Metabase + BigQuery |
| E-commerce | Shopee, Lazada, Tiki | Tableau/Looker + BigQuery |
| Enterprise | Vingroup, Masan, FPT | Power BI + SQL Server |
| MNCs | Unilever, Nestlé, Samsung | Tableau/Power BI |
| Consulting | Deloitte, PwC, EY | Power BI + various |

### JD mẫu — Mid BI Developer (Banking)

```
Yêu cầu:
- 2-4 năm kinh nghiệm Power BI / Tableau
- Expert DAX, data modeling (star schema)
- SQL thành thạo (Redshift/SQL Server)
- Hiểu ETL/data pipeline concepts
- RLS, workspace governance
- Tiếng Anh giao tiếp

Mô tả:
- Thiết kế enterprise data models cho reporting
- Build interactive dashboards cho C-level
- Implement RLS cho 20+ chi nhánh
- Performance optimization (<5s load time)
- Training business users
- Collaborate với DE team

Lương: 30-45 triệu + bonus 2-4 tháng
```

### Kênh tìm việc & Tips

| Kênh | Best for |
|---|---|
| ITViec | Nhiều JD BI nhất |
| LinkedIn "Power BI Vietnam" group | Networking, referral |
| Power BI Community VN (Facebook) | Jobs, tips, learning |
| Tableau User Group Vietnam | Tableau-specific |
| TopDev | Startup/tech companies |

### Hiring Trends BI (2025-2026)

| Trend | Impact |
|---|---|
| Power BI dominant tại VN banking | PL-300 cert = strong advantage |
| AI Copilot trong Power BI | Cần hiểu NLQ, prompt design |
| Fabric adoption starting | Early adopters có competitive edge |
| Self-service demand tăng | BI shift từ "build" sang "enable" |
| Semantic model standard | LookML/MetricFlow/Cube trở nên phổ biến |

---

## 11. Learning Schedule Templates

> PL-300 là mục tiêu rất tốt cho 12 tuần part-time. Cert này vừa giúp bạn structured learning, vừa là "proof" cho nhà tuyển dụng. Đừng overthink — chọn 1 template, bắt đầu ngay hôm nay. 2h/ngày là đủ. Mỗi dashboard bạn build đều là một bước tiến.

### Template A: Part-time (2h/ngày)

**12 tuần — Power BI Fundamentals → PL-300 ready**

| Tuần | Focus | Practice |
|---|---|---|
| 1-2 | Power BI Desktop basics, data import | Build 2 simple reports |
| 3-4 | DAX fundamentals: measures, calculated columns | 20 DAX exercises |
| 5-6 | Data modeling: star schema, relationships | Design 2 models |
| 7-8 | DAX advanced: time intelligence, filter context | Pattern library |
| 9-10 | Power BI Service: publish, refresh, RLS | Deploy 1 workspace |
| 11-12 | PL-300 exam prep: practice tests | Mock exam ≥80% |

**Daily schedule (weekday):**
| Slot | Activity |
|---|---|
| 20:00-20:30 | Review yesterday + read theory |
| 20:30-21:30 | Hands-on practice |
| 21:30-22:00 | Note-taking, plan tomorrow |

**Weekend:** 3h Saturday deep-dive project work

### Template B: Full-time intensive (6 tuần)

| Tuần | Mon-Wed | Thu-Fri | Sat |
|---|---|---|---|
| 1 | SQL + Data modeling | Power BI basics | Mini project |
| 2 | DAX fundamentals | Viz best practices | Dashboard v1 |
| 3 | DAX advanced | Performance tuning | Optimize dashboard |
| 4 | Power Query + ETL | RLS + Governance | Enterprise model |
| 5 | Project: multi-source dashboard | Docs + deployment | Portfolio polish |
| 6 | PL-300 prep | Mock exams | Exam day |

### Progress Tracking

```
| Week | Course % | DAX exercises | Dashboards built | PL-300 mock score |
|------|----------|---------------|------------------|-------------------|
| W1   | 15%      | 5             | 1 (basic)        | -                 |
| W2   | 30%      | 15            | 1 (improved)     | -                 |
| W3   | 50%      | 30            | 2                | -                 |
| ...  | ...      | ...           | ...              | 65% → 80%        |
```
