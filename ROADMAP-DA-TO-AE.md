# Career Roadmap: Data Analyst → BI Engineer → Analytics Engineer

## Bối cảnh

Roadmap phát triển career path cho team Data, định hướng từ vai trò Data Analyst / BI Engineer sang Analytics Engineer (AE) — vai trò kết hợp kỹ năng engineering + analytics.

---

## Phase 1: Data Analyst (Foundation)

**Mục tiêu:** Thành thạo phân tích dữ liệu, SQL nâng cao, business understanding.

### Hard Skills

| Skill | Công cụ / Tech | Level cần đạt |
|-------|----------------|:-------------:|
| SQL nâng cao | Redshift, PostgreSQL | ⭐⭐⭐⭐ |
| Data exploration & profiling | DBeaver, Redshift Query Editor | ⭐⭐⭐ |
| Excel / Google Sheets | Pivot, VLOOKUP, Power Query | ⭐⭐⭐ |
| Statistics cơ bản | Descriptive stats, correlation | ⭐⭐⭐ |
| Python cho analysis | pandas, numpy, matplotlib | ⭐⭐⭐ |
| Business domain | E-commerce, SaaS, or Banking operations | ⭐⭐⭐ |

### Deliverables

- [ ] Viết được complex queries (window functions, CTEs, subqueries)
- [ ] Tự explore data source mới, document schema/business logic
- [ ] Tạo ad-hoc reports cho business stakeholders
- [ ] Hiểu data pipeline flow: source → staging → golden → curated

---

## Phase 2: BI Engineer (Visualization + Modeling)

**Mục tiêu:** Xây dựng dashboards, data modeling cho reporting, self-service analytics.

### Hard Skills

| Skill | Công cụ / Tech | Level cần đạt |
|-------|----------------|:-------------:|
| BI Tool mastery | Superset / QuickSight / Metabase | ⭐⭐⭐⭐ |
| Dimensional modeling | Star schema, snowflake, SCD | ⭐⭐⭐⭐ |
| Data visualization | Chart selection, dashboard design | ⭐⭐⭐⭐ |
| Semantic layer | Metrics definitions, KPI catalog | ⭐⭐⭐ |
| Performance tuning | Query optimization, materialization | ⭐⭐⭐ |
| Stakeholder communication | Requirements gathering, presentation | ⭐⭐⭐⭐ |

### Deliverables

- [ ] Design + deploy 3+ production dashboards
- [ ] Dimensional model cho 1 business domain (ví dụ: orders/customers trong E-commerce, subscriptions trong SaaS, hoặc transactions/lending trong Fintech)
- [ ] Self-service data catalog / metrics layer
- [ ] KPI documentation cho business teams
- [ ] Dashboard performance optimization (< 5s load time)

---

## Phase 3: Analytics Engineer (Switch Track)

**Mục tiêu:** Kết hợp engineering rigor + analytics thinking. Own toàn bộ transformation layer.

### Tại sao switch?

| DA/BI Engineer | Analytics Engineer |
|----------------|-------------------|
| Query data → make reports | Build the data models that reports use |
| Use dashboards | Build + maintain the pipeline feeding dashboards |
| Ad-hoc analysis | Systematic, tested, version-controlled transformations |
| Dependent on DE for data | Self-serve: own staging → golden → curated |
| Tools: Excel/BI | Tools: dbt + SQL + Git + CI/CD |

### Hard Skills

| Skill | Công cụ / Tech | Level cần đạt |
|-------|----------------|:-------------:|
| dbt (core) | dbt-core + dbt-redshift | ⭐⭐⭐⭐⭐ |
| Data modeling (advanced) | Incremental, snapshot, SCD Type 2 | ⭐⭐⭐⭐ |
| Testing & quality | dbt tests, great_expectations, data contracts | ⭐⭐⭐⭐ |
| Git workflow | Branching, MR review, CI/CD | ⭐⭐⭐⭐ |
| Python engineering | Clean code, modules, automation scripts | ⭐⭐⭐ |
| Infrastructure | Airflow orchestration, Redshift admin | ⭐⭐⭐ |
| Data governance | Lineage, metadata, masking, RBAC | ⭐⭐⭐⭐ |
| Documentation | dbt docs, Confluence, decision records | ⭐⭐⭐⭐ |

### Deliverables

- [ ] Own 1 full domain pipeline end-to-end (source → curated)
- [ ] Implement incremental models với proper testing
- [ ] Setup data quality monitoring (freshness, row count, schema drift)
- [ ] Contribute to data governance (masking, RBAC, metadata)
- [ ] Code review dbt models từ team members
- [ ] Write technical specs / design docs cho features mới
- [ ] Mentor junior DA trên SQL + dbt basics

---

## Phase 4: SWE Best Practices (Engineering Excellence)

**Mục tiêu:** Áp dụng Software Engineering best practices vào data work — nâng chất lượng code, quy trình, và collaboration.

### 4.0 Environments & Branching Strategy

#### Môi trường

| Environment | Mục đích | Ai dùng | Data |
|-------------|----------|---------|------|
| **LOCAL** | Dev cá nhân, chạy dbt compile/test | Developer | Kết nối SIT Redshift |
| **SIT** (System Integration Testing) | Dev chung, test features mới | DE/AE team | Data thật từ core/source systems (subset hoặc full) |
| **UAT** (User Acceptance Testing) | Mirror PROD, dùng cho `--defer` | Tester + BA | Data sync từ PROD |
| **PROD** (Production) | Live, phục vụ BI/business | Airflow automated | Data nghiệp vụ thật |

#### Branching Model

```
                    ┌─── feature/new-model ───┐
                    │                         │
                    ├─── feature/fix-bug ─────┤
                    │                         ▼
    ┌───────────────┤                    ┌─────────┐
    │               │         MR         │   sit   │ ← CI/CD auto deploy lên SIT Redshift
    │               │       + review     └────┬────┘
    │               │                         │
    │               │                    MR + approval
    │               │                         ▼
    │               │                    ┌─────────┐
    │               │                    │   uat   │ ← Mirror PROD (defer state)
    │               │                    └────┬────┘
    │               │                         │
    │               │                    Release approval
    │               │                         ▼
    │               │                    ┌─────────┐
    │               │                    │  master │ ← PROD deploy (Airflow)
    │               │                    └─────────┘
    └───────────────┘
```

#### Branch → Environment Mapping

| Branch | Deploy target | Trigger | Ai merge |
|--------|--------------|---------|----------|
| `feature/*` | — (chỉ local test) | — | Developer tạo |
| `sit` | SIT Redshift | Auto (merge MR) | Developer + 1 reviewer |
| `uat` | UAT Redshift | Auto (merge từ sit) | Lead approval |
| `master` | PROD Redshift | Auto (merge từ uat) | Manager approval |

#### Quy tắc

1. **KHÔNG push trực tiếp** vào `sit`, `uat`, `master` — phải qua MR
2. **Feature branch** tạo từ `sit`, merge về `sit`
3. **Hotfix** tạo từ `master`, cherry-pick ngược về `sit`/`uat`
4. **Mỗi MR phải có** ít nhất 1 reviewer approve
5. **SIT → UAT → PROD** — code chỉ flow 1 chiều lên
6. **Không skip environment** — không merge feature trực tiếp vào master

#### Environment Variables per Branch

| Env var | SIT | UAT | PROD |
|---------|-----|-----|------|
| `DB_HOST` | `redshift-sit...` | `redshift-uat...` | `redshift-prod...` |
| `DB_USER` | `dbadmin` | `dbadmin_uat` | `airflow_dwh` |
| `DBT_TARGET` | `sit` | `uat` | `prod` |
| Redshift schemas | Shared (`analytics_shared`) | Shared | Shared |
| Data freshness | Daily source sync | Mirror PROD | Real-time source sync |

#### Áp dụng cho từng repo

| Repo | Branch chính | Deploy trigger |
|------|-------------|----------------|
| `dwh-airflow-dbt` | `sit` → `uat` → `master` | Merge vào `sit` → dbt run on SIT |
| `dwh-redshift-rbac` | `sit` | Merge vào `sit` → RBAC deploy on SIT |

---

### 4.1 Version Control & Git Workflow

| Practice | Mô tả | Áp dụng |
|----------|--------|---------|
| Trunk-based development | Feature branches ngắn, merge thường xuyên | dbt models, scripts |
| Meaningful commits | `feat:`, `fix:`, `refactor:`, `docs:` prefix | Tất cả repos |
| Pull Request / MR review | Mọi thay đổi phải qua review trước merge | Bắt buộc |
| Branch protection | Nhánh `sit`/`master` không push trực tiếp | GitLab settings |
| .gitignore hygiene | Không commit secrets, compiled files | .env, __pycache__ |

### 4.2 Code Quality & Standards

| Practice | Mô tả | Áp dụng |
|----------|--------|---------|
| DRY (Don't Repeat Yourself) | Dùng macros/CTEs thay vì copy-paste SQL | dbt macros |
| Single Responsibility | 1 model = 1 transformation logic | Staging/Golden/Curated separation |
| Naming conventions | `stg_`, `cst_`, `cur_` prefix nhất quán | Model naming |
| Code formatting | SQLFluff cho SQL, Black/Ruff cho Python | Lint trước commit |
| Comments & docstrings | Giải thích WHY, không giải thích WHAT | SQL models + Python |
| Configuration as Code | Env vars, YAML configs — không hardcode | .env, dbt_project.yml |

### 4.3 Testing Strategy

| Level | Loại test | Áp dụng |
|-------|-----------|---------|
| Unit test | dbt test (unique, not_null, accepted_values) | Mọi model |
| Integration test | dbt run + test on SIT | Trước merge |
| Smoke test | Verify deployed state (roles, permissions, DDM) | Post-deploy |
| Regression test | Compare output trước/sau refactor | Khi refactor models |
| Contract test | Schema + data type validation | `data_type` in .yml |

```
Testing Pyramid cho Data:
                    ┌─────────┐
                    │  Smoke  │  ← ít nhất, chạy sau deploy
                    ├─────────┤
                 ┌──┤  Integ  ├──┐  ← dbt run + test (SIT)
                 │  ├─────────┤  │
              ┌──┤  │  Unit   │  ├──┐  ← nhiều nhất (dbt tests)
              │  │  └─────────┘  │  │
              └──┴───────────────┴──┘
```

### 4.4 CI/CD & Automation

| Practice | Mô tả | Áp dụng |
|----------|--------|---------|
| Pipeline as Code | `.gitlab-ci.yml` / Jenkinsfile trong repo | Cả 2 repos |
| Automated deploy | Merge → auto deploy (không manual) | RBAC, DDM |
| Idempotent operations | Chạy lại không lỗi | SQL scripts |
| Environment separation | SIT / UAT / PROD riêng biệt | Roles + connections |
| Secret management | Env vars / Vault — không commit | .env in .gitignore |
| Rollback strategy | Git revert → redeploy | IaC approach |

### 4.5 Documentation & Communication

| Practice | Mô tả | Áp dụng |
|----------|--------|---------|
| README.md mỗi repo | Setup, usage, troubleshooting | Bắt buộc |
| ADR (Architecture Decision Records) | Document quyết định kỹ thuật | `.kiro/specs/` |
| Changelog | Track thay đổi theo version | CHANGELOG.md |
| Runbook | Step-by-step cho operations | Confluence |
| Code as documentation | dbt docs, model .yml descriptions | Mọi model |
| Diagram as Code | Mermaid trong markdown | Design docs |

### 4.6 Observability & Monitoring

| Practice | Mô tả | Áp dụng |
|----------|--------|---------|
| Logging | Structured logs cho scripts | Python logging |
| Alerting | Notify khi pipeline fail | Airflow alerts |
| Data freshness | Monitor khi data cũ quá | dbt source freshness |
| Drift detection | Phát hiện schema/permission thay đổi ngoài ý muốn | Audit queries |
| Cost monitoring | Track Redshift query cost | CloudWatch |

### 4.7 Security Best Practices

| Practice | Mô tả | Áp dụng |
|----------|--------|---------|
| Least Privilege | Chỉ grant quyền cần thiết | RBAC 12 roles |
| No shared credentials | 1 user = 1 account | Redshift users |
| Credential rotation | Đổi password định kỳ | Policy enforcement |
| Audit trail | Mọi thay đổi quyền qua MR | Git history |
| Input validation | Sanitize identifiers trong scripts | `sanitize_identifier()` |
| PII protection | DDM + hash + RBAC layers | Defense in depth |

### SWE Deliverables

- [ ] Enforce SQLFluff lint trên mọi MR (CI check)
- [ ] 100% dbt models có file .yml với tests
- [ ] README.md đầy đủ cho mỗi repo
- [ ] MR template với checklist (tests pass, docs updated, reviewer assigned)
- [ ] Automated deploy cho cả RBAC + DDM
- [ ] Monitoring dashboard cho pipeline health
- [ ] Security review quarterly (audit permissions, rotate credentials)

---

## Skill Comparison Matrix

| Skill | DA | BI Engineer | Analytics Engineer | AE + SWE |
|-------|:--:|:-----------:|:------------------:|:--------:|
| SQL | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Python | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| dbt | — | — | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Git | ⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| BI Tools | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| Data Modeling | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| CI/CD | — | — | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Airflow | — | — | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Testing | ⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Business acumen | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| Documentation | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Data Governance | ⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Code Review | — | — | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Security | ⭐ | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Observability | — | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |

---

## Timeline đề xuất

```
Month 1-3:   DA Foundation (SQL mastery, business domain)
Month 4-6:   BI Engineering (dashboards, dimensional modeling)
Month 7-9:   Transition → AE (dbt basics, Git workflow, first models)
Month 10-12: AE Core (own pipeline, testing, governance)
Month 12+:   AE Senior (mentoring, architecture, cross-team)
```

---

## Resources

### dbt & Analytics Engineering
- [dbt Learn (Free courses)](https://courses.getdbt.com/) — Fundamentals, Advanced, Jinja
- [dbt Best Practices](https://docs.getdbt.com/best-practices) — style guide, project structure
- [dbt-redshift docs](https://docs.getdbt.com/docs/core/connect-data-platform/redshift-setup)
- [Analytics Engineering Roundup](https://roundup.getdbt.com/) — weekly newsletter
- [dbt Discourse](https://discourse.getdbt.com/) — community Q&A
- [How we structure our dbt projects (dbt Labs)](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)

### SQL
- [Advanced SQL (Mode Analytics)](https://mode.com/sql-tutorial/) — window functions, subqueries
- [SQLBolt](https://sqlbolt.com/) — interactive SQL exercises
- [Use The Index, Luke](https://use-the-index-luke.com/) — SQL performance tuning
- [Redshift SQL Reference](https://docs.aws.amazon.com/redshift/latest/dg/cm_chap_SQLCommandRef.html)
- [Window Functions cheat sheet](https://learnsql.com/blog/sql-window-functions-cheat-sheet/)

### Data Modeling
- [The Data Warehouse Toolkit (Kimball)](https://www.kimballgroup.com/) — dimensional modeling bible
- [One Big Table vs Star Schema](https://benn.substack.com/p/the-case-against-the-data-warehouse)
- [Data Modeling for Analytics Engineering (dbt)](https://docs.getdbt.com/terms/dimensional-modeling)
- [Slowly Changing Dimensions (SCD)](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-1-2-3/)

### Python
- [Python for Data Engineers](https://realpython.com/) — practical tutorials
- [Effective Python (Brett Slatkin)](https://effectivepython.com/) — 90 best practices
- [Clean Code in Python (Packt)](https://www.packtpub.com/product/clean-code-in-python) — maintainable code
- [Pydantic docs](https://docs.pydantic.dev/) — data validation
- [psycopg2 docs](https://www.psycopg.org/docs/) — Redshift/PostgreSQL driver

### Git & Version Control
- [Pro Git Book (free)](https://git-scm.com/book/en/v2) — comprehensive Git guide
- [Conventional Commits](https://www.conventionalcommits.org/) — commit message format
- [Git branching strategies](https://www.atlassian.com/git/tutorials/comparing-workflows)
- [GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html) — environment-based branching

### CI/CD & DevOps
- [GitLab CI/CD docs](https://docs.gitlab.com/ee/ci/)
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
- [12 Factor App](https://12factor.net/) — methodology cho modern apps
- [Infrastructure as Code patterns](https://www.hashicorp.com/resources/what-is-infrastructure-as-code)

### Testing
- [dbt Testing best practices](https://docs.getdbt.com/docs/build/data-tests)
- [Great Expectations](https://greatexpectations.io/) — data quality framework
- [Data Contracts](https://datacontract.com/) — schema + SLA agreements
- [Test Pyramid (Martin Fowler)](https://martinfowler.com/articles/practical-test-pyramid.html)

### Data Governance & Security
- [Data Governance 101](https://www.dataversity.net/what-is-data-governance/)
- [Redshift DDM docs](https://docs.aws.amazon.com/redshift/latest/dg/t_ddm.html)
- [Redshift RBAC docs](https://docs.aws.amazon.com/redshift/latest/dg/t_Roles.html)
- [OWASP Data Security](https://owasp.org/www-project-data-security/)
- [GDPR for engineers](https://gdpr.eu/what-is-gdpr/)

### BI & Visualization
- [Apache Superset docs](https://superset.apache.org/docs/intro)
- [Amazon QuickSight User Guide](https://docs.aws.amazon.com/quicksight/)
- [Storytelling with Data (Cole Nussbaumer)](http://www.storytellingwithdata.com/)
- [The Big Book of Dashboards](https://www.bigbookofdashboards.com/)

### Airflow & Orchestration
- [Apache Airflow docs](https://airflow.apache.org/docs/)
- [Astronomer Cosmos (dbt + Airflow)](https://astronomer.github.io/astronomer-cosmos/)
- [Data Pipeline Design Patterns](https://www.oreilly.com/library/view/data-pipelines-pocket/9781492087823/)

### Software Engineering Culture
- [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/) — must-read
- [Locally Optimistic](https://locallyoptimistic.com/) — data team culture blog
- [Data Engineering Weekly](https://www.dataengineeringweekly.com/) — newsletter
- [Martin Fowler's blog](https://martinfowler.com/) — architecture, refactoring, testing patterns
- [Staff Engineer (Will Larson)](https://staffeng.com/) — career growth beyond senior
- [Team Topologies](https://teamtopologies.com/) — team organization

### Newsletters & Communities (subscribe)
- [Analytics Engineering Roundup](https://roundup.getdbt.com/) — weekly
- [Data Engineering Weekly](https://www.dataengineeringweekly.com/) — weekly
- [Seattle Data Guy](https://seattledataguy.substack.com/) — career + technical
- [Benn Stancil's Substack](https://benn.substack.com/) — data strategy
- [r/dataengineering](https://reddit.com/r/dataengineering) — community
- [dbt Slack](https://getdbt.slack.com/) — 50k+ members

---

## Đánh giá tiến độ

| Milestone | Criteria | Timeframe |
|-----------|----------|-----------|
| DA Ready | Complex SQL, ad-hoc reports, domain knowledge | Month 3 |
| BI Ready | 3 production dashboards, dimensional model | Month 6 |
| AE Junior | First dbt model in production, Git workflow | Month 9 |
| AE Mid | Own 1 domain pipeline, testing, code review | Month 12 |
| AE Senior | Architecture decisions, mentoring, governance | Month 18+ |
