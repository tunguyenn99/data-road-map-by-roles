# ⚙️ BI Engineer — Career Roadmap

> *Nếu BI Developer là người xây ngôi nhà đẹp, thì BI Engineer là người đặt nền móng, kéo điện nước, và đảm bảo ngôi nhà đứng vững qua mưa bão. Không ai khen BI Engineer khi pipeline chạy smooth — nhưng ai cũng tìm bạn khi dashboard load chậm 30 giây, khi data sáng nay không refresh, khi metric hôm qua bị sai. Bạn là người "đằng sau sân khấu" — khi CEO nhìn dashboard load trong 2 giây với data fresh mỗi sáng, họ không biết phía sau có ai đó đang optimize distribution keys, tune incremental refresh, và setup CI/CD cho Power BI deployments. Nếu bạn thích xây hệ thống mà người khác dựa vào mỗi ngày, nếu bạn cảm thấy thỏa mãn khi infrastructure chạy invisible nhưng reliable — BI Engineer là con đường của bạn.*

## 1. Tổng quan vai trò

**BI Engineer** là hybrid giữa Data Engineer và BI Developer. Vai trò này tập trung vào việc xây dựng và tối ưu hóa data infrastructure phía sau hệ thống BI — bao gồm ETL pipelines, data warehouse modeling, semantic layers, và CI/CD cho analytics platform.

### Phân biệt BI Engineer vs BI Developer vs Data Engineer

| Tiêu chí | BI Developer | BI Engineer | Data Engineer |
|---|---|---|---|
| Focus | Visualization & reports | BI infrastructure & pipelines | General data platform |
| Primary output | Dashboards | Optimized data layer cho BI | Data pipelines & platform |
| Core tools | Power BI, Tableau | dbt + Warehouse + BI tool | Spark, Airflow, Kafka |
| Data modeling | Semantic/report model | Physical + semantic model | Physical model |
| Code depth | DAX/LookML | SQL + Python + IaC | Python + Scala/Java |
| Users served | Business | BI team + Business | Data team + downstream |

### Responsibilities chính
- Thiết kế & maintain data warehouse schema (Kimball/Data Vault)
- Xây dựng & optimize ETL/ELT pipelines feeding BI layer
- Quản lý semantic layer (dbt metrics, LookML, Cube.js)
- Implement data quality checks & monitoring
- CI/CD pipelines cho BI deployments (Power BI / Tableau / Looker)
- Performance tuning: query optimization, materialization strategy
- Data governance: lineage, catalog, access control
- Bridge giữa Data Engineering team và BI/Analytics team

---

## 2. Skill Matrix theo Level

> BI Engineer cần skill set rộng hơn BI Developer: bạn không chỉ biết DAX — bạn cần biết cả Python, Airflow, CI/CD, và cloud infrastructure. Nghe nhiều? Đừng lo. Không ai giỏi hết từ đầu. Bảng dưới giúp bạn biết ưu tiên gì ở mỗi level — đừng cố học hết cùng lúc, hãy xây từng tầng.

### Technical Skills

| Skill | Intern | Fresher | Junior | Mid | Senior |
|---|:---:|:---:|:---:|:---:|:---:|
| SQL | Basic queries | Complex JOINs, CTEs | Window fn, optimization | Query plan analysis | Architecture design |
| Python | Basics | Scripting, Pandas | ETL scripts, APIs | Frameworks, testing | System design |
| Data Warehouse | Concept | Star schema | SCD, bridge tables | Data Vault, modeling patterns | Enterprise architecture |
| dbt | - | Project structure | Models, tests, docs | Macros, packages, incremental | Framework design |
| Cloud DW (Redshift/BQ/Snowflake) | - | Basic queries | Distribution, sort keys | Cost optimization | Multi-cluster strategy |
| ETL/Orchestration | - | Understand concepts | Airflow DAGs | Complex orchestration | Platform design |
| BI Tool (Power BI/Tableau) | - | Basic usage | Dataflows, datasets | Deployment pipelines | Governance & DevOps |
| CI/CD & DevOps | - | Git basics | PR workflow | Pipeline automation | Full GitOps |
| Data Quality | - | - | Basic tests | Framework design | Observability platform |
| IaC (Terraform/CloudFormation) | - | - | - | Basic resources | Full infrastructure |

### Soft Skills

| Skill | Intern | Fresher | Junior | Mid | Senior |
|---|:---:|:---:|:---:|:---:|:---:|
| Cross-team Communication | - | Respond | Proactive | Lead discussions | Architecture decisions |
| Documentation | Note-taking | READMEs | Technical guides | ADRs, runbooks | Standards & frameworks |
| Problem Decomposition | Follow steps | Defined problems | Break into subtasks | Design solutions | Define approach |
| Project Management | - | - | Task tracking | Sprint planning | Roadmap ownership |
| Mentoring | - | - | - | Guide juniors | Build team capability |

---

## 3. Learning Path chi tiết

> Khác với BI Developer học tool trước, BI Engineer học infrastructure trước. SQL + Data Warehouse là nền tảng, dbt là vũ khí chính, rồi Airflow + CI/CD là thứ biến bạn từ "người viết query" thành "người vận hành platform." Kiên nhẫn — mỗi layer xây lên đều có ý nghĩa. Giống xây nhà: móng trước, tường sau, mái cuối.

### Phase 1: Foundation (Intern → Fresher) — 0-6 tháng

**Mục tiêu:** SQL fluent, hiểu data warehouse concepts

| Tuần | Chủ đề | Output |
|---|---|---|
| 1-3 | SQL mastery: complex joins, aggregations, CTEs | Query portfolio |
| 4-6 | Python scripting: file processing, APIs, Pandas | ETL scripts |
| 7-9 | Data warehouse concepts: Kimball methodology, star schema | Model diagrams |
| 10-12 | Cloud basics: AWS/GCP fundamentals, Redshift/BigQuery intro | Cloud lab setup |
| 13-16 | Version control: Git workflow, branching strategy | Collaborative project |
| 17-20 | BI tool deep-dive: data layer (datasets, dataflows) | Data model in BI tool |
| 21-24 | Mini project: design + load star schema from raw data | Portfolio piece #1 |

### Phase 2: Core Competency (Fresher → Junior) — 6-12 tháng

**Mục tiêu:** Build data pipelines feeding BI, dbt proficiency

| Tuần | Chủ đề | Output |
|---|---|---|
| 1-4 | dbt fundamentals: models, sources, tests, docs | dbt project |
| 5-8 | dbt advanced: incremental models, macros, packages | Production patterns |
| 9-12 | Airflow basics: DAGs, operators, scheduling | Orchestrated pipeline |
| 13-16 | Data quality: dbt tests, great expectations, monitoring | Quality framework |
| 17-20 | CI/CD for analytics: GitHub Actions, pre-commit, sqlfluff | Automated pipeline |
| 21-24 | Project: full ELT pipeline → dbt → BI dashboard | Portfolio piece #2 |

### Phase 3: Advanced (Junior → Mid) — 12-24 tháng

**Mục tiêu:** Architect BI platform, optimize at scale

| Tháng | Chủ đề | Output |
|---|---|---|
| 1-2 | Advanced warehouse: distribution styles, sort keys, workload mgmt | Optimization report |
| 3-4 | Semantic layer: dbt metrics, MetricFlow, Cube.js | Metrics platform |
| 5-6 | Data governance: lineage, catalog (OpenMetadata, Atlan) | Governance setup |
| 7-8 | Advanced orchestration: dynamic DAGs, SLA, alerting | Production Airflow |
| 9-10 | BI DevOps: Power BI Git integration, Tableau migration | CI/CD for BI |
| 11-12 | Performance at scale: materialized views, caching, query patterns | Performance playbook |

### Phase 4: Expert (Mid → Senior) — 24+ tháng

**Mục tiêu:** Platform vision, cross-org influence

| Tháng | Chủ đề | Output |
|---|---|---|
| 1-3 | Data platform architecture: lakehouse, data mesh principles | Architecture blueprint |
| 4-6 | Cost engineering: compute optimization, storage tiering | Cost reduction plan |
| 7-9 | Real-time BI: streaming, CDC, near-real-time refresh | Real-time architecture |
| 10-12 | AI-enhanced BI: automated insights, NLQ, vector search | AI integration strategy |
| Ongoing | Technical leadership, vendor evaluation, hiring | Platform & team growth |

---

## 4. Resources

> BI Engineering nằm ở giao điểm của nhiều domains — nên resources cũng đa dạng: từ Kimball book (data modeling) đến Astronomer Academy (Airflow) đến dbt docs (transformation). Đừng cố đọc hết — chọn theo phase bạn đang ở. Mỗi phase có 2-3 resources "must-read", còn lại là "nice-to-have."

### Courses (Verified ✓)

| Course | Platform | Level | Link |
|---|---|---|---|
| dbt Fundamentals | dbt Learn | Beginner | [Link](https://courses.getdbt.com/courses/fundamentals) |
| dbt Advanced Materializations | dbt Learn | Mid | [Link](https://courses.getdbt.com/courses/advanced-materializations) |
| Analytics Engineering with dbt | DataCamp | Mid | [Link](https://www.datacamp.com/courses/introduction-to-dbt) |
| Data Warehouse Concepts (Kimball) | Udemy | Beginner-Mid | [Link](https://www.udemy.com/course/data-warehouse-fundamentals-for-beginners/) |
| AWS Data Analytics Specialty | AWS Training | Mid-Senior | [Link](https://aws.amazon.com/certification/certified-data-analytics-specialty/) |
| Snowflake SnowPro Core | Snowflake | Mid | [Link](https://www.snowflake.com/certifications/) |
| Apache Airflow Fundamentals | Astronomer | Mid | [Link](https://academy.astronomer.io/) |
| Data Engineering Zoomcamp | DataTalks.Club | All (Free) | [Link](https://github.com/DataTalksClub/data-engineering-zoomcamp) |

### Books

| Sách | Tác giả | Level |
|---|---|---|
| The Data Warehouse Toolkit (3rd Ed) | Ralph Kimball | ⭐ Must-read |
| Fundamentals of Data Engineering | Joe Reis & Matt Housley | Mid |
| Data Pipelines Pocket Reference | James Densmore | Mid |
| Building a Scalable Data Warehouse with Data Vault 2.0 | Daniel Linstedt | Senior |
| Designing Data-Intensive Applications | Martin Kleppmann | Senior |
| Analytics Engineering with SQL and dbt | Rui Machado & Hélder Russa | Mid |

### Certifications

| Cert | Tổ chức | Giá trị |
|---|---|---|
| dbt Analytics Engineering Certification | dbt Labs | ⭐ Core identity |
| AWS Data Analytics Specialty | AWS | Cloud + data |
| Snowflake SnowPro Core | Snowflake | Modern warehouse |
| Google Professional Data Engineer | GCP | Comprehensive |
| Microsoft DP-203 (Data Engineering) | Microsoft | Azure ecosystem |
| Astronomer Certification for Apache Airflow | Astronomer | Orchestration |

### Communities & Practice

| Resource | Link |
|---|---|
| dbt Community (Slack + Discourse) | [community.getdbt.com](https://community.getdbt.com/) |
| DataTalks.Club | [datatalks.club](https://datatalks.club/) |
| r/dataengineering | [reddit.com/r/dataengineering](https://www.reddit.com/r/dataengineering/) |
| Seattle Data Guy (blog) | [seattledataguy.substack.com](https://seattledataguy.substack.com/) |
| Modern Data Stack Blog | [moderndatastack.xyz](https://www.moderndatastack.xyz/) |
| Locally Optimistic (analytics community) | [locallyoptimistic.com](https://locallyoptimistic.com/) |

---

## 5. Portfolio Projects gợi ý

> Portfolio BI Engineer cần show "end-to-end pipeline thinking" — không phải chỉ 1 dashboard, mà cả flow: source → dbt → warehouse → BI tool → monitoring. Nếu bạn publish 1 repo có Airflow + dbt + dbt docs + CI/CD + performance benchmark — bạn sẽ nổi bật hơn 90% candidates. Đó là bằng chứng bạn "nghĩ hệ thống."

### Intern/Fresher Level
1. **Star Schema Design** — Thiết kế + load dimensional model từ normalized source
2. **SQL Performance Lab** — Benchmark queries, explain plans, optimize
3. **Git Workflow for SQL** — Version control cho SQL scripts

### Junior Level
4. **dbt Project End-to-End** — Sources → staging → marts → docs → tests
5. **Automated Data Pipeline** — Airflow + dbt + data quality checks
6. **BI Dataset Optimization** — Redesign dataset cho Power BI DirectQuery performance

### Mid Level
7. **Semantic Layer Implementation** — dbt metrics / MetricFlow + downstream consumption
8. **Data Catalog Setup** — OpenMetadata / Atlan integration với dbt
9. **CI/CD for Analytics** — GitHub Actions + dbt Cloud + slim CI + deployment

### Senior Level
10. **Data Platform Blueprint** — Full architecture: ingestion → warehouse → BI → governance
11. **Real-time BI Pipeline** — CDC + streaming → materialized views → live dashboard
12. **Cost Optimization Project** — Analyze warehouse spend, implement efficiency measures

---

## 6. Interview Prep

> Interview BI Engineer thường kết hợp câu hỏi data modeling + system design. Họ muốn biết bạn không chỉ "viết dbt model chạy đúng" mà còn "thiết kế pipeline chịu tải cho tổ chức." Sẵn sàng vẽ architecture diagram trên whiteboard — đó là khoảnh khắc bạn tỏa sáng. Và nhớ: luôn giải thích "tại sao" chứ không chỉ "cái gì."

### Technical Questions (by level)

**Fresher/Junior:**
- SCD Type 1 vs Type 2 — giải thích và SQL implementation
- dbt: source freshness là gì? Khi nào cần?
- Star schema: fact table vs dimension table
- Incremental model: append vs merge vs delete+insert

**Mid:**
- Design data model cho hệ thống banking transactions (billions rows)
- dbt project đang mất 2h để chạy — optimize thế nào?
- Airflow DAG dependency management cho 200+ dbt models
- Data quality incident: downstream dashboard sai — incident response?

**Senior:**
- Data mesh: domain-oriented ownership impact lên BI infrastructure
- Migrate from legacy ETL (SSIS/Informatica) sang modern stack — plan?
- Real-time vs batch BI — khi nào cần real-time? Trade-offs?
- BI platform selection: Power BI vs Tableau vs Looker — evaluation framework

### System Design Questions
1. **Design a metric layer** — Multi-tool consumption, consistent definitions
2. **Design data freshness monitoring** — SLA tracking, alerting, incident response
3. **Design BI deployment pipeline** — Dev → UAT → Prod, rollback strategy

---

## 7. Salary Benchmark (VN Market 2025-2026)

> BI Engineer là "sweet spot" về salary — technical depth cao hơn BI Developer, nhưng không cần deep Spark/Kafka như Data Engineer. dbt skill là differentiator lớn nhất tại VN. Banking sector đang trả premium cho ai có "dbt + Redshift + Airflow" combo. Nếu bạn có cả 3, inbox LinkedIn sẽ không bao giờ yên.

| Level | YoE | Salary Range (VND/tháng) | Note |
|---|---|---|---|
| Intern | 0 | 4-8 triệu | Lab + learning |
| Fresher | 0-1 | 10-18 triệu | SQL + basic tools |
| Junior | 1-2 | 15-28 triệu | dbt + pipeline |
| Mid | 2-4 | 25-50 triệu | Architecture |
| Senior | 4-7+ | 45-85 triệu | Platform ownership |
| Lead/Principal | 7+ | 60-120+ triệu | Strategy + team |

> *BI Engineer demand đang tăng mạnh tại VN (banking, fintech, e-commerce). dbt skill là differentiator lớn nhất. Salary cao hơn BI Developer ~20-30% do technical depth.*

---

## 8. Career Progression Paths từ BI Engineer

> BI Engineer senior tự nhiên sẽ trở thành platform thinker. Từ đây, bạn có thể chọn đi sâu vào Data Platform (Spark, Kafka), đi rộng sang Solutions Architect, hoặc lead team. Overlap với Analytics Engineer rất lớn — nhiều người switch qua lại giữa 2 roles tùy context.

```
BI Engineer Senior ─┬─→ Staff/Principal BI Engineer
                    ├─→ Data Platform Engineer
                    ├─→ Analytics Engineer (overlap lớn)
                    ├─→ Engineering Manager (Data)
                    └─→ Solutions Architect
```


---

## 9. Day-in-the-Life Stories

> Muốn biết BI Engineer khác BI Developer ở đâu? Nhìn vào daily routine: BI Dev mở Power BI Desktop, BI Engineer mở terminal + VS Code + Airflow UI. Cùng serve business — nhưng ở layer khác nhau. Nếu bạn thấy hứng thú hơn khi debug query plan thay vì chọn màu biểu đồ — welcome aboard.

### Junior BI Engineer — Fintech

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:30 | Check dbt run results: 2 tests failed | dbt Cloud, Slack |
| 9:00 | Debug: `unique` test fail trên customer_dim → duplicate keys | DBeaver, SQL |
| 9:30 | Fix: add deduplication logic trong staging model | VS Code, dbt |
| 10:00 | Standup: report blocker, ask AE about source data | Teams |
| 10:30 | Write new staging model cho payment transactions | VS Code, SQL |
| 11:30 | Add schema tests: not_null, unique, accepted_values | YAML |
| 13:00 | Create PR, run CI checks (sqlfluff + dbt test) | GitHub, GitHub Actions |
| 14:00 | PR review từ senior: "Add incremental logic" | GitHub |
| 14:30 | Implement incremental: append strategy + partition | VS Code, dbt |
| 15:30 | Local test: `dbt run -s +model_name --target dev` | Terminal |
| 16:00 | Update documentation: model description, column docs | dbt docs |
| 16:30 | Self-study: Kimball book chapter 5 (SCD) | Book |

> **Cảm nhận:** "Daily workflow = code + test + PR + learn. dbt is life."

### Senior BI Engineer — Banking

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:00 | Review overnight Airflow DAGs, check SLA compliance | Airflow UI |
| 8:30 | Investigate: golden model took 45min (usually 10min) | Redshift Query History |
| 9:00 | Root cause: distribution key mismatch → broadcast join | EXPLAIN |
| 9:30 | Fix: change distribution style, test on dev | dbt, Redshift |
| 10:00 | Architecture review: new source integration plan | Miro, Teams |
| 11:00 | Design data model cho new loan product | dbdiagram.io |
| 13:00 | Code review: junior's PR (3 models + tests) | GitHub |
| 13:30 | Mentor session: explain incremental strategies | Whiteboard |
| 14:30 | Semantic layer: MetricFlow metric definitions | dbt, YAML |
| 15:30 | OpenMetadata lineage integration | Python, API |
| 16:30 | Write ADR: "Why append over merge for transactions" | Confluence |
| 17:00 | Sprint planning prep | Jira |

> **Cảm nhận:** "Mix of deep technical + architecture + mentoring. Own the platform, not just models."

---

## 10. Vietnam Job Market Analysis

> "BI Engineer" tại VN thường ẩn dưới nhiều title: "Analytics Engineer", "Data Engineer (BI)", hoặc đơn giản là "BI Developer" nhưng JD đòi dbt + Airflow. Đọc kỹ JD, đừng chỉ nhìn title. Nếu thấy "dbt + warehouse + CI/CD" — đó là BI Engineer job.

### Top Companies tuyển BI Engineer tại VN

| Tier | Companies | Stack |
|---|---|---|
| Banking | VPBank, Techcombank, MBBank, VIB | dbt + Redshift + Airflow |
| Fintech | MoMo, ZaloPay, Timo | dbt + BigQuery + Airflow |
| E-commerce | Shopee, Lazada, Tiki | dbt/Spark + BigQuery |
| Tech | VNG, Grab, Gojek | dbt + Snowflake/BigQuery |
| Outsource/Consulting | Infinite Lambda, Holistics, NAB (VN) | dbt + various DW |

### JD mẫu — Middle Analytics/BI Engineer

```
(Ref: Infinite Lambda Vietnam, Holistics)

Yêu cầu:
- 2+ năm kinh nghiệm với dbt + SQL
- Cloud data warehouse (Redshift/BigQuery/Snowflake)
- Data modeling: Kimball dimensional modeling
- CI/CD: Git, GitHub Actions / GitLab CI
- Testing: dbt tests, data quality frameworks
- Nice-to-have: Airflow, Python, LookML

Mô tả:
- Design & build automated analytics pipelines
- Model, transform, test, document data using dbt
- Collaborate with engineers and client teams
- Performance optimization on cloud DW
- CI/CD pipeline maintenance

Lương: 25-50 triệu (tùy level + company)
Remote: Nhiều positions hybrid/full-remote
```

### Hiring Trends

| Trend | Impact |
|---|---|
| dbt adoption tăng mạnh (banking + fintech) | dbt cert = instant interview |
| Remote work từ VN cho global companies | Higher salary range (USD-based) |
| Infinite Lambda, Indicium, etc. hiring VN | Outsource AE/BI Eng growing |
| Modern data stack standard | Snowflake/BQ + dbt + Airflow = golden combo |
| DataOps culture | CI/CD skills differentiator |

---

## 11. Learning Schedule Templates

> Transition sang BI Engineer cần kiên nhẫn hơn BI Developer vì scope rộng hơn. 16 tuần part-time hoặc 8 tuần full-time — đủ để có portfolio basic. Nhưng thật sự "production-ready" cần thêm 3-6 tháng on-the-job. Đừng chờ hoàn hảo mới apply — apply khi đủ foundation, rồi học tiếp trong job.

### Template A: Part-time (2h/ngày) — DA/BI → BI Engineer transition

**16 tuần plan**

| Tuần | Focus (2h/ngày) | Weekend project |
|---|---|---|
| 1-2 | Git workflow: branch, PR, merge | Setup personal repo |
| 3-4 | dbt Fundamentals course | Jaffle Shop project |
| 5-6 | dbt testing + docs | Add tests to project |
| 7-8 | Incremental models | Convert 3 models to incremental |
| 9-10 | Airflow basics | Simple DAG triggering dbt |
| 11-12 | CI/CD: GitHub Actions | Automated pipeline |
| 13-14 | Cloud DW deep-dive (Redshift/BQ) | Query optimization lab |
| 15-16 | Portfolio project: end-to-end pipeline | Deploy + document |

### Template B: Full-time intensive (8 tuần)

| Tuần | Morning (3h) | Afternoon (3h) | Output |
|---|---|---|---|
| 1 | SQL advanced + DW concepts | Git + terminal | Query portfolio |
| 2 | dbt Fundamentals | Hands-on: first project | dbt cert prep |
| 3 | dbt Advanced: incremental, macros | Build staging layer | 10 models |
| 4 | dbt testing + packages | Quality framework | Test suite |
| 5 | Airflow fundamentals | dbt + Airflow integration | Running DAG |
| 6 | CI/CD: Actions + sqlfluff | Automated pipeline | Full CI/CD |
| 7 | Cloud DW: Redshift optimization | Performance tuning | Playbook |
| 8 | Portfolio polish + docs | Interview prep | Ready! |

### Weekly tracking template

```
| Week | dbt models written | Tests added | PRs merged | Concepts mastered |
|------|-------------------|-------------|------------|-------------------|
| W1   | 0 (setup)         | 0           | 0          | Git, CLI          |
| W2   | 3 staging         | 5           | 1          | dbt refs, sources |
| W3   | 5 staging + 2 mart| 12          | 2          | Incremental       |
| ...  | ...               | ...         | ...        | ...               |
```
