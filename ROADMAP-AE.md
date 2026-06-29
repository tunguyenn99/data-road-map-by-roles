# 🔧 Analytics Engineer (AE) — Career Roadmap

> *Analytics Engineer là role mới nhất trong thế giới data — sinh ra từ nỗi đau thực sự. Trước đây, DA viết query ad-hoc, DE build pipeline — không ai sở hữu cái ở giữa. Kết quả? Metric không nhất quán, logic trùng lặp, bugs phát hiện muộn. AE ra đời để giải quyết đúng vấn đề đó. Nếu Data Engineer xây đường ống, Data Analyst tìm vàng cuối đường ống — thì Analytics Engineer là người xây nhà máy lọc: biến quặng thô thành vàng ròng, có kiểm định chất lượng, có quy trình, có documentation. AE là role trẻ nhất trong data nhưng đang trở thành "backbone" của mọi modern data team. Bạn không chỉ viết SQL — bạn xây hệ thống mà cả tổ chức tin tưởng.*

## 1. Tổng quan vai trò

**Analytics Engineer** là bridge giữa Data Engineering và Data Analysis. AE sở hữu transformation layer — biến raw data thành trusted, modeled datasets mà toàn bộ tổ chức có thể sử dụng. AE áp dụng software engineering best practices (version control, testing, CI/CD, documentation) vào analytics code.

### Vị trí trong Data Team

```
Data Engineer → [Raw Data] → Analytics Engineer → [Modeled Data] → Data Analyst/BI
     ↓                              ↓                                    ↓
Ingestion, Platform          Transform, Model, Test              Analysis, Insight
```

### Responsibilities chính
- Thiết kế & maintain data models (staging → intermediate → marts)
- Viết & tối ưu SQL transformations (dbt)
- Implement data tests & quality monitoring
- Build semantic layer / metrics definitions
- Document data models cho downstream consumers
- Own data lineage & impact analysis
- CI/CD cho analytics code
- Collaborate: upstream (DE) cho data sources, downstream (DA/BI) cho requirements
- Data governance: ownership, access control, PII handling

### Typical Day
| Thời gian | Hoạt động |
|---|---|
| 8:30 - 9:30 | Check dbt run results, data quality alerts |
| 9:30 - 11:00 | Code: new models, refactor existing, write tests |
| 11:00 - 12:00 | PR review, collaborate với DA on data needs |
| 13:00 - 15:00 | Deep work: complex modeling, performance optimization |
| 15:00 - 16:00 | Documentation, metric definitions |
| 16:00 - 17:30 | Sprint ceremonies, stakeholder sync |

---

## 2. Skill Matrix theo Level

> Khác với DA, skill matrix của AE nặng về "engineering discipline" hơn. Bạn không chỉ cần viết SQL đúng — bạn cần viết SQL có test, có docs, có CI/CD. Mỗi level là một bước từ "người code" sang "người thiết kế hệ thống." Nếu bạn từng frustrated vì "query đúng nhưng không ai maintain" — AE mindset sẽ giải phóng bạn.

### Technical Skills

| Skill | Intern | Fresher | Junior | Mid | Senior |
|---|:---:|:---:|:---:|:---:|:---:|
| SQL | Basics | JOINs, CTEs, Window fn | Optimization, advanced | Architecture-aware | Design patterns |
| dbt | - | Project structure, models | Tests, docs, sources | Macros, packages, incremental | Framework design, custom materializations |
| Data Modeling | - | Star schema concept | Kimball, SCD | Data Vault, advanced patterns | Enterprise modeling |
| Python | Basics | Scripting | dbt packages, automation | Testing frameworks | Platform tools |
| Git & CI/CD | - | Commit, push | Branching, PR | Pipeline design | GitOps strategy |
| Cloud DW (Redshift/BQ/Snowflake) | - | Basic queries | Distribution, performance | Cost optimization | Architecture |
| Data Quality | - | Understand tests | Write custom tests | Quality framework | Observability platform |
| Semantic Layer | - | - | Use metrics | Design MetricFlow/Cube | Enterprise metrics |
| Orchestration (Airflow) | - | - | Understand DAGs | Configure, troubleshoot | Design patterns |
| Data Governance | - | - | Docs, lineage | Ownership, classification | Framework design |

### Soft Skills

| Skill | Intern | Fresher | Junior | Mid | Senior |
|---|:---:|:---:|:---:|:---:|:---:|
| Business Understanding | - | Domain basics | Metrics meaning | Define metrics | Strategy alignment |
| Documentation | Notes | README | Model docs, wiki | Standards, ADRs | Knowledge platform |
| Collaboration | Pair work | Team tasks | Cross-function | Lead initiatives | Org-wide influence |
| Code Review | - | Receive | Give + receive | Design feedback | Architecture review |
| Mentoring | - | - | - | Guide juniors | Build team practices |

---

## 3. Learning Path chi tiết

> Con đường của AE giống như xây một tòa nhà: trước tiên phải đổ móng SQL + dbt, rồi dựng khung testing + CI/CD, rồi mới bắt đầu thiết kế kiến trúc. Đừng vội nhảy vào Data Vault khi còn chưa viết nổi một incremental model chạy đúng. Solid foundation > fancy patterns. Và nhớ: "dbt is just SQL" — nhưng discipline xung quanh nó mới là thứ thay đổi cuộc chơi.

### Phase 1: Foundation (Intern → Fresher) — 0-6 tháng

**Mục tiêu:** SQL mastery, dbt basics, understand data pipeline

| Tuần | Chủ đề | Output |
|---|---|---|
| 1-3 | SQL deep-dive: JOINs, window fn, CTEs, subqueries | 30+ practice queries |
| 4-6 | Git workflow: branches, PRs, merge conflicts | Collaborative project |
| 7-9 | dbt fundamentals: models, sources, refs, fresh | dbt Learn certificate |
| 10-12 | Data warehouse concepts: facts, dimensions, star schema | Model diagram |
| 13-16 | dbt testing: schema tests, data tests, custom tests | Test suite |
| 17-20 | dbt docs: descriptions, docs blocks, lineage | Documented project |
| 21-24 | Mini project: build staging + marts layer from raw | Portfolio piece #1 |

### Phase 2: Core Competency (Fresher → Junior) — 6-12 tháng

**Mục tiêu:** Build production-ready dbt project, own data models

| Tuần | Chủ đề | Output |
|---|---|---|
| 1-4 | Incremental models: append, merge, delete+insert | Incremental patterns |
| 5-8 | dbt macros & Jinja: DRY principles, reusable code | Macro library |
| 9-12 | Data modeling patterns: SCD, snapshots, bridge tables | Complex model |
| 13-16 | dbt packages: dbt-utils, dbt-expectations, codegen | Package usage |
| 17-20 | CI/CD: GitHub Actions + sqlfluff + dbt slim CI | Pipeline setup |
| 21-24 | Project: full dbt project with tests, docs, CI/CD | Portfolio piece #2 |

### Phase 3: Advanced (Junior → Mid) — 12-24 tháng

**Mục tiêu:** Design data architecture, own metrics layer

| Tháng | Chủ đề | Output |
|---|---|---|
| 1-2 | Advanced modeling: Data Vault 2.0, activity schema | Pattern library |
| 3-4 | Semantic layer: MetricFlow, dbt metrics, Cube.js | Metrics layer |
| 5-6 | Performance: warehouse-specific optimization | Performance playbook |
| 7-8 | Orchestration: Airflow/Dagster + dbt integration | Production pipeline |
| 9-10 | Data governance: lineage, catalog, quality SLAs | Governance setup |
| 11-12 | Cross-domain modeling: multi-team, data mesh influence | Domain model |

### Phase 4: Expert (Mid → Senior) — 24+ tháng

**Mục tiêu:** Platform design, define data strategy

| Tháng | Chủ đề | Output |
|---|---|---|
| 1-3 | Analytics platform architecture: end-to-end design | Architecture blueprint |
| 4-6 | AI-ready data modeling: context engineering, feature stores | AI integration |
| 7-9 | Data mesh implementation: domain ownership, contracts | Mesh framework |
| 10-12 | Technical leadership: hiring, standards, culture | Team building |
| Ongoing | Conference talks, open source contribution, thought leadership | Community impact |

---

## 4. Resources

> AE community là một trong những community mở và helpful nhất trong tech. dbt Slack có hàng ngàn người sẵn sàng giúp bạn debug macro lúc 2h sáng. Tận dụng tối đa — đừng học một mình khi cả một cộng đồng đang đợi bạn hỏi. Và "dbt Discourse" là nơi bạn thấy senior AE giải thích architecture decisions — học không thua sách nào.

### Courses (Verified ✓)

| Course | Platform | Level | Link |
|---|---|---|---|
| dbt Fundamentals | dbt Learn | Beginner | [Link](https://courses.getdbt.com/courses/fundamentals) |
| dbt Advanced Materializations | dbt Learn | Mid | [Link](https://courses.getdbt.com/courses/advanced-materializations) |
| Jinja, Macros, Packages | dbt Learn | Mid | [Link](https://courses.getdbt.com/courses/jinja-macros-packages) |
| Analytics Engineering with dbt | Udemy | Beginner-Mid | [Link](https://www.udemy.com/course/complete-dbt-data-build-tool-bootcamp/) |
| Data Warehouse Fundamentals | Udemy | Beginner | [Link](https://www.udemy.com/course/data-warehouse-fundamentals-for-beginners/) |
| The Complete dbt Bootcamp | Udemy | All | [Link](https://www.udemy.com/course/complete-dbt-data-build-tool-bootcamp/) |
| Data Engineering Zoomcamp | DataTalks.Club | All (Free) | [Link](https://github.com/DataTalksClub/data-engineering-zoomcamp) |
| Analytics Engineering Guide | dbt Docs | All (Free) | [Link](https://docs.getdbt.com/guides/best-practices) |

### Books

| Sách | Tác giả | Level |
|---|---|---|
| Analytics Engineering with SQL and dbt | Rui Machado & Hélder Russa | ⭐ Core |
| The Data Warehouse Toolkit (3rd Ed) | Ralph Kimball | Mid-Senior |
| Fundamentals of Data Engineering | Joe Reis & Matt Housley | Mid |
| Building a Scalable Data Warehouse with Data Vault 2.0 | Daniel Linstedt | Senior |
| Data Mesh | Zhamak Dehghani | Senior |
| Designing Data-Intensive Applications | Martin Kleppmann | Senior |

### Certifications

| Cert | Tổ chức | Giá trị |
|---|---|---|
| dbt Analytics Engineering Certification | dbt Labs | ⭐⭐ Must-have |
| Snowflake SnowPro Core | Snowflake | Cloud DW |
| AWS Data Analytics Specialty | AWS | Cloud platform |
| Google Professional Data Engineer | GCP | Platform breadth |
| Astronomer Certification (Airflow) | Astronomer | Orchestration |

### Key Blogs & Resources

| Resource | Link |
|---|---|
| dbt Developer Blog | [getdbt.com/blog](https://www.getdbt.com/blog/) |
| dbt Best Practices | [docs.getdbt.com/best-practices](https://docs.getdbt.com/best-practices) |
| Analytics Engineering Guide (dbt) | [docs.getdbt.com/guides](https://docs.getdbt.com/guides/) |
| Locally Optimistic | [locallyoptimistic.com](https://locallyoptimistic.com/) |
| The Analytics Engineering Roundup (newsletter) | Substack |
| analyticsengineering.com | [analyticsengineering.com](https://www.analyticsengineering.com/) |
| State of Analytics Engineering (annual report) | [getdbt.com/resources](https://www.getdbt.com/resources/state-of-analytics-engineering-2025) |

### Communities

| Resource | Link |
|---|---|
| dbt Community (Slack) | [community.getdbt.com](https://community.getdbt.com/) |
| dbt Discourse | [discourse.getdbt.com](https://discourse.getdbt.com/) |
| DataTalks.Club | [datatalks.club](https://datatalks.club/) |
| Locally Optimistic Slack | [locallyoptimistic.com](https://locallyoptimistic.com/) |
| r/dataengineering | [reddit.com/r/dataengineering](https://www.reddit.com/r/dataengineering/) |

---

## 5. Portfolio Projects gợi ý

> Trong thế giới AE, portfolio = GitHub repo. Không cần slide đẹp, không cần PowerPoint. Một repo dbt có README rõ ràng, tests chạy xanh, docs generate ra đẹp — đó là "CV" mạnh nhất của bạn. Hãy viết code như thể senior đang review PR của bạn ngay lúc này.

### Intern/Fresher Level
1. **dbt Starter Project** — Staging + marts từ public dataset (Jaffle Shop extended)
2. **SQL Window Functions Lab** — 20 challenges với real scenarios
3. **Git Workflow Practice** — Branching, PRs, conflict resolution

### Junior Level
4. **Full dbt Project** — Sources → staging → intermediate → marts → docs → tests → CI
5. **Incremental Models** — Handle late-arriving data, backfill strategy
6. **dbt + Airflow Integration** — Orchestrated runs, freshness checks

### Mid Level
7. **Semantic Layer** — MetricFlow / Cube.js metrics cho org
8. **Data Quality Framework** — Custom tests, SLA monitoring, alerting
9. **Multi-team dbt Project** — Domain ownership, cross-refs, packages

### Senior Level
10. **Analytics Platform Design** — Full architecture: ingestion → transform → serve
11. **Data Mesh Transformation** — Domain-oriented dbt mono-repo → multi-repo
12. **AI Context Layer** — dbt models designed for LLM consumption (context engineering)

---

## 6. Interview Prep

> Interview AE khác DA ở chỗ: người ta muốn thấy bạn "nghĩ như engineer." Không chỉ query đúng — mà cần giải thích tại sao chọn incremental thay vì full-refresh, tại sao tách model ra 3 layers thay vì gộp 1. Hãy sẵn sàng vẽ architecture trên whiteboard. Và luôn giải thích trade-offs — đó là dấu hiệu của người có kinh nghiệm thật.

### Technical Questions (by level)

**Fresher/Junior:**
- ref() vs source() trong dbt — khi nào dùng cái nào?
- Incremental model: giải thích is_incremental() macro
- Star schema: fact vs dimension table — design cho e-commerce orders
- dbt test: schema test vs data test — ví dụ?

**Mid:**
- Project có 500 models, chạy 3h — optimization strategy?
- Design data model cho SCD Type 2 với dbt snapshots
- Slim CI: giải thích và implement
- Handling late-arriving facts trong incremental model

**Senior:**
- Data mesh: khi nào split mono-repo dbt thành multi-repo?
- Semantic layer strategy: centralized metrics vs domain metrics
- AI & analytics engineering: context engineering là gì? Ảnh hưởng đến modeling?
- Build AE team from 0: hiring, onboarding, standards, culture

### System Design Questions
1. **Design transformation layer** cho banking app: transactions, customers, products
2. **Design data quality framework**: automated testing, monitoring, alerting, SLA
3. **Design CI/CD pipeline** cho dbt: dev → staging → prod, rollback strategy

---

## 7. Salary Benchmark (VN Market 2025-2026)

> AE là role "khan hiếm" nhất trong data tại VN lúc này. Supply thấp, demand cao — nghĩa là bạn có bargaining power. Nhưng salary chỉ reflect đúng khi bạn có portfolio chứng minh. Một cert dbt + GitHub repo solid = lời mời phỏng vấn không ngừng. Đầu tư 3-6 tháng nghiêm túc, ROI sẽ rất rõ ràng.

| Level | YoE | Salary Range (VND/tháng) | Note |
|---|---|---|---|
| Intern | 0 | 4-8 triệu | Learning dbt + SQL |
| Fresher | 0-1 | 10-18 triệu | dbt basics |
| Junior | 1-2 | 15-30 triệu | Independent models |
| Mid | 2-4 | 28-55 triệu | Architecture ownership |
| Senior | 4-7+ | 45-90 triệu | Platform strategy |
| Staff/Principal | 7+ | 65-130+ triệu | Org-wide influence |

> *AE là role mới nhất trong data, supply cực thấp → salary premium 20-40% so với DA cùng level. dbt certification là strong signal. Banking/fintech/e-commerce trả cao nhất.*

---

## 8. Career Progression Paths từ AE

> AE senior không phải đỉnh — mà là bệ phóng. Từ đây bạn có thể chọn đi sâu vào platform engineering, rẽ ngang sang data architecture, hoặc lead cả team. AE là role cho người thích xây dựng — và khi hệ thống đủ phức tạp, bạn sẽ tự nhiên trở thành architect.

```
AE Senior ─┬─→ Staff/Principal AE
           ├─→ Data Platform Engineer
           ├─→ Head of Analytics Engineering
           ├─→ Data Architect
           └─→ Engineering Manager (Data)
```

---

## 9. AE trong 2026 và beyond

> Năm 2026, AE không chỉ viết SQL nữa — họ thiết kế hệ thống cho cả con người lẫn AI consume. Tương lai không đợi ai. AI đang thay đổi cách chúng ta code, test, và thậm chí design models. Nếu bạn bắt đầu bây giờ, bạn sẽ là người định hình role này — chứ không phải người chạy theo. "dbt changed the game once. Context engineering is changing it again."

Theo [dbt Labs State of Analytics Engineering 2025-2026](https://www.getdbt.com/resources/state-of-analytics-engineering-2025):

### 3 responsibilities mới của AE:
1. **System Designer** — Focus vào how the system of models works, domain boundaries, metric consistency
2. **Governance Owner** — Data contracts, quality SLAs, lineage
3. **AI Context Provider** — Cung cấp trusted context cho LLM/AI systems

### AI impact:
- 72% AE đã dùng AI-assisted coding (copilot, AI PR review)
- AI handles boilerplate → AE focuses on design & governance
- Mới: **Context Engineering** — design models cho AI consumption

### Key trends:
- Semantic layer adoption tăng mạnh
- Data contracts between domains
- Shift from model production → system design
- Data quality = product quality


---

## 10. Day-in-the-Life Stories

> Lý thuyết là một chuyện — thực tế ngồi code dbt 8 tiếng trông ra sao? Dưới đây là 3 ngày điển hình ở 3 level. Bạn sẽ thấy rõ: level càng cao, code càng ít, design + communicate càng nhiều. Nhưng ở mọi level, cảm giác merge PR và thấy CI xanh — luôn thỏa mãn.

### Fresher AE — Startup

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 9:00 | Pull latest code, check CI on main | Git, GitHub |
| 9:30 | Pick up ticket: "Add staging model cho Stripe payments" | Jira |
| 10:00 | Read source docs, understand schema | Stripe docs |
| 10:30 | Write `stg_stripe__payments.sql`: clean, rename, cast | VS Code, dbt |
| 11:00 | Add YAML config: tests, descriptions | VS Code |
| 11:30 | Local run: `dbt run -s stg_stripe__payments` | Terminal |
| 13:00 | Fix: column type mismatch | VS Code |
| 13:30 | Add source freshness check | YAML |
| 14:00 | Create PR, write description | GitHub |
| 14:30 | Senior reviews: "Add custom test for amount > 0" | GitHub |
| 15:00 | Add custom test, push fix | VS Code, Git |
| 15:30 | PR merged! Check CI deployment | GitHub Actions |
| 16:00 | Document existing customer models | dbt docs |
| 16:30 | Self-study: incremental models | dbt docs |

> **Cảm nhận:** "Daily = code → test → PR → deploy. Fast feedback loop. dbt community helps a lot."

### Mid AE — E-commerce / Banking

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:30 | Check Airflow DAG results: all green ✅ | Airflow UI |
| 9:00 | Standup: update on customer mart refactoring | Teams |
| 9:30 | Design: break monolithic model → 3 intermediate + 1 mart | dbdiagram.io |
| 10:30 | Implement SCD2 with merge strategy | dbt, SQL |
| 11:30 | Write reusable macro: SCD2 pattern for team | Jinja |
| 13:00 | Meeting với DA: "What metrics do you need?" | Teams |
| 13:30 | Define MetricFlow metrics: GMV, AOV, order_count | YAML |
| 14:30 | Performance investigation: query timeout | EXPLAIN |
| 15:00 | Fix: add sort key, change dist style | dbt config |
| 15:30 | Update data quality tests: volume checks | dbt-expectations |
| 16:00 | Code review: junior's first incremental model | GitHub |
| 16:30 | Write ADR: modeling decisions for new domain | dbt docs |

> **Cảm nhận:** "Own 1-2 domains. Balance between deep technical work và cross-team collaboration."

### Senior AE — Platform scale

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:00 | Review: dbt Cloud job failures across 5 domains | dbt Cloud |
| 8:30 | Design: data contract between payment & customer domains | YAML, Notion |
| 9:30 | Architecture decision: mono-repo vs multi-repo for data mesh | Miro |
| 10:30 | Implement: custom materialization for slowly-updating dims | Jinja, Python |
| 11:30 | Present: "Context Engineering for AI" to data leadership | Slides |
| 13:00 | Interview: mid AE candidate | Zoom |
| 14:00 | Define: analytics platform roadmap Q3 | Notion |
| 15:00 | Open source: contribute fix to dbt-utils package | GitHub |
| 16:00 | 1:1 mentoring: help junior design their first mart | VS Code, Slack |
| 17:00 | Write: blog post on semantic layer patterns | Notion |

> **Cảm nhận:** "Less model writing, more system design + governance + team building. AI-readiness is the new frontier."

---

## 11. Vietnam Job Market Analysis

> AE là role đang ở giai đoạn "early majority" tại VN. Nhiều company gọi bạn là "BI Engineer" hay "Data Engineer" nhưng thực ra bạn làm AE work. Hiểu điều này giúp bạn không bỏ lỡ cơ hội chỉ vì title khác. Đọc JD kỹ — nếu thấy "dbt + modeling + testing" đó là AE.

### AE Demand tại VN (2025-2026)

- AE là role **newest** trong data — JD xuất hiện ~2022 tại VN
- Supply cực thấp → **premium salary** so với DA/DE cùng YoE
- Nhiều companies gọi role khác (BI Engineer, Data Engineer) nhưng làm AE work

### Top Companies tuyển AE/dbt

| Company | Role title | Stack |
|---|---|---|
| Infinite Lambda (VN) | Analytics Engineer | dbt + BQ/Snowflake |
| Holistics | Analytics Engineer | dbt + Holistics platform |
| Grab | Analytics Engineer | dbt + BigQuery |
| MoMo | Data Engineer (AE work) | dbt + Redshift |
| Techcombank | BI/Analytics Engineer | dbt + Redshift + Airflow |
| Shopee | Data Analyst (modeling) | dbt-like + BigQuery |
| Remote global | AE (full-remote VN) | dbt + Snowflake + Fivetran |

### JD mẫu — Analytics Engineer (Remote, global)

```
(Ref: Infinite Lambda, Analytics Engineering openings for VN)

Requirements:
- 2+ years SQL + dbt experience
- Strong data modeling (Kimball methodology)
- Git workflow, CI/CD (GitHub Actions)
- Cloud DW: BigQuery / Snowflake / Redshift
- dbt tests, documentation, packages
- Good English communication (client-facing)

Nice to have:
- MetricFlow / Semantic layer
- Airflow / Dagster orchestration
- Python scripting
- Looker / LookML

Compensation: $2,000-4,000 USD/month (remote for global client)
= 50-100 triệu VND
```

### Hiring Trends

| Trend | Impact |
|---|---|
| dbt certification demand | Must-have for serious AE candidates |
| Remote work boom | VN AE can work for EU/US companies |
| AI + AE convergence | Context engineering = new skill |
| Data contracts emerging | AE owns interface definitions |
| Semantic layer standardization | MetricFlow/Cube.js knowledge valuable |

---

## 12. Learning Schedule Templates

> Học dbt không phải đọc docs — mà là viết code mỗi ngày. 30 phút/ngày commit vào GitHub còn giá trị hơn đọc 3h sách vào cuối tuần. Consistency beats intensity. Và mỗi commit = mỗi bước tiến, dù nhỏ.

### Template A: Part-time (2h/ngày) — DA transitioning to AE

**20 tuần plan (xem chi tiết tại ROADMAP-SWITCH-DA-TO-AE.md)**

| Tuần | Focus (2h/ngày weekday + 3h weekend) | Milestone |
|---|---|---|
| 1-3 | Git + CLI fundamentals | Comfortable with terminal |
| 4-6 | dbt Fundamentals course | dbt Learn certificate |
| 7-9 | Build first dbt project (10 models) | GitHub repo |
| 10-12 | Testing + documentation | 100% test coverage |
| 13-15 | Incremental models + macros | Production patterns |
| 16-18 | CI/CD setup | Automated pipeline |
| 19-20 | Portfolio polish + cert prep | Ready to apply |

### Template B: Full-time intensive (10 tuần)

| Tuần | Focus | Daily schedule (8h) | Output |
|---|---|---|---|
| 1 | SQL mastery | 4h practice + 4h theory | Query portfolio |
| 2 | Git + dbt setup | 3h Git + 5h dbt basics | First dbt project |
| 3 | dbt models: staging | 8h hands-on modeling | 8 staging models |
| 4 | dbt models: marts | 8h hands-on modeling | 5 mart models |
| 5 | Testing + docs | 4h tests + 4h documentation | Full test suite |
| 6 | Incremental + macros | 6h implementation + 2h theory | Production patterns |
| 7 | CI/CD + deployment | 8h pipeline setup | Automated CI |
| 8 | Airflow integration | 6h Airflow + 2h integration | Running DAG |
| 9 | Portfolio project | 8h building | Complete project |
| 10 | Cert prep + interviews | 4h study + 4h mock interviews | Certified! |

### Tracking spreadsheet

```
| Week | Models written | Tests | PRs | dbt commands mastered | Cert prep % |
|------|---------------|-------|-----|----------------------|-------------|
| W1   | 0             | 0     | 0   | run, test            | 0%          |
| W2   | 3             | 5     | 1   | run, test, docs      | 10%         |
| W3   | 8             | 15    | 2   | + source, seed       | 20%         |
| W4   | 13            | 25    | 3   | + snapshot           | 30%         |
| ...  | 25+           | 50+   | 8+  | All core commands    | 80%+        |
```
