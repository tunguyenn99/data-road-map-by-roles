# 🔄 Switch từ Data Analyst → Analytics Engineer — Transition Roadmap

> *Có lẽ bạn đang ngồi đó, viết query SQL giỏi hơn ai hết trong team, nhưng thấy thiếu gì đó. Metric mỗi người query một kiểu. File SQL nằm scattered. Không ai review code. Bạn tự hỏi: có cách nào tốt hơn không? Nếu bạn đang là DA và cảm thấy "mình cứ query hoài cùng một logic, sao không ai build sẵn cho mình?" — chúc mừng, bạn đang nghĩ như một Analytics Engineer. Tin tốt: 80% kỹ năng bạn cần cho AE, bạn đã có sẵn từ DA. Transition từ DA sang AE không phải nhảy sang thế giới khác — mà là đi sâu hơn vào thế giới bạn đã ở. Bạn đã có SQL, đã hiểu business, đã biết end-user cần gì. Bây giờ bạn chỉ cần thêm engineering discipline để biến những query ad-hoc thành một hệ thống đáng tin cậy.*

## 1. Tổng quan transition

### Tại sao DA → AE là natural path?

Analytics Engineer là evolution tự nhiên của DA vì:
- DA đã có SQL strong → AE cần SQL deeper
- DA hiểu business context → AE translate business logic vào code
- DA biết end-user cần gì → AE build models phục vụ đúng nhu cầu
- DA thấy data quality pain → AE solve data quality systematically

### What changes?

| Dimension | Data Analyst | Analytics Engineer |
|---|---|---|
| **Mindset** | "Tôi dùng data" | "Tôi build hệ thống data" |
| **Output** | Insight, report, dashboard | Code, models, pipelines |
| **Tooling** | BI tool + SQL + Python | dbt + SQL + Git + CI/CD |
| **Code practices** | Script/notebook (ad-hoc) | Version control, testing, docs |
| **Stakeholders** | Business users | DA + BI + DE teams |
| **Quality ownership** | Spot-check | Systematic testing & monitoring |
| **Lifecycle** | Query → Analyze → Present | Model → Test → Deploy → Monitor |

### Skill Gap Analysis: DA → AE

```
✅ Đã có (từ DA):                    🔲 Cần học thêm:
├── SQL (SELECT, JOIN, Window)        ├── dbt (core identity)
├── Business logic understanding      ├── Git workflow (branching, PR)
├── Data interpretation               ├── CI/CD (automation)
├── Stakeholder communication         ├── Data modeling (Kimball formal)
├── Problem framing                   ├── Testing practices
├── Python basics                     ├── Infrastructure basics
└── BI tool familiarity               └── Software engineering mindset
```

---

## 2. Transition Timeline: 6-12 tháng

> 6-12 tháng nghe nhiều, nhưng thực ra mỗi tháng bạn sẽ "level up" rõ rệt. Tháng 1 bạn sợ Git, tháng 3 bạn thoải mái tạo PR, tháng 6 bạn deploy model production. Mỗi milestone nhỏ đều là chiến thắng — hãy celebrate nó. Và nhớ: bạn không bắt đầu từ zero — bạn bắt đầu từ nền tảng DA đã có. 6 tháng từ giờ, bạn có thể là người deploy dbt model đầu tiên của team.

### Month 1-2: Git + Software Engineering Fundamentals

**Mục tiêu:** Chuyển từ "write scripts" sang "write maintainable code"

| Tuần | Chủ đề | Practice |
|---|---|---|
| 1 | Git basics: init, commit, push, pull | Version control daily SQL work |
| 2 | Branching: feature branches, merge, conflicts | Practice on personal project |
| 3 | PR workflow: review, feedback, iterate | Open PRs on team repo |
| 4 | Code quality: linting (sqlfluff), formatting | Add linter to workflow |
| 5-6 | Software eng principles: DRY, modularity, naming | Refactor old SQL scripts |
| 7-8 | Terminal/CLI basics, environment setup | Comfortable with command line |

**Resources:**
- [Git for Data People (free)](https://github.com/DataTalksClub/data-engineering-zoomcamp)
- [Oh Shit, Git!?!](https://ohshitgit.com/)
- [sqlfluff docs](https://docs.sqlfluff.com/)

### Month 3-4: dbt Core

**Mục tiêu:** Build dbt project từ scratch, hiểu fundamental concepts

| Tuần | Chủ đề | Practice |
|---|---|---|
| 1-2 | dbt Fundamentals course | Complete dbt Learn certificate |
| 3-4 | Project structure: models, sources, refs | Migrate 5 DA queries sang dbt models |
| 5-6 | Materializations: view, table, incremental | Convert existing queries |
| 7-8 | Testing: schema tests, custom tests | Add tests cho mỗi model |

**Key shift:** Thay vì viết query chạy 1 lần → viết model deploy nhiều lần

**Resources:**
- [dbt Fundamentals (free course)](https://courses.getdbt.com/courses/fundamentals)
- [dbt Best Practices](https://docs.getdbt.com/best-practices)
- [Jaffle Shop tutorial](https://github.com/dbt-labs/jaffle_shop)

### Month 5-6: Data Modeling Formal

**Mục tiêu:** Hiểu & apply modeling patterns chuyên nghiệp

| Tuần | Chủ đề | Practice |
|---|---|---|
| 1-2 | Kimball dimensional modeling: facts, dimensions | Design model cho current work |
| 3-4 | SCD (Slowly Changing Dimensions): Type 1, 2 | Implement SCD with dbt snapshots |
| 5-6 | Naming conventions, layer architecture (staging/marts) | Refactor dbt project |
| 7-8 | Performance: incremental strategies, late-arriving data | Optimize existing models |

**Key shift:** Thay vì "query cho đúng kết quả" → "design model cho reusable, performant, trustworthy"

**Resources:**
- Kimball's Data Warehouse Toolkit (chapters 1-4)
- [dbt Modeling Guide](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)
- [Analytics Engineering with SQL and dbt (book)](https://www.oreilly.com/library/view/analytics-engineering-with/9781098142377/)

### Month 7-8: Testing & Documentation

**Mục tiêu:** Guarantee data quality systematically

| Tuần | Chủ đề | Practice |
|---|---|---|
| 1-2 | dbt tests deep-dive: built-in, custom, packages | 100% test coverage cho project |
| 3-4 | dbt-expectations, dbt-utils packages | Advanced test patterns |
| 5-6 | Documentation: descriptions, docs blocks | Full model documentation |
| 7-8 | Data quality monitoring: freshness, volume, schema | Alerting setup |

**Key shift:** DA finds data issues after the fact → AE prevents data issues before they reach consumers

**Resources:**
- [dbt-expectations package](https://github.com/calogica/dbt-expectations)
- [dbt testing best practices](https://docs.getdbt.com/docs/build/tests)

### Month 9-10: CI/CD & Automation

**Mục tiêu:** Deploy code confidently, automate repetitive work

| Tuần | Chủ đề | Practice |
|---|---|---|
| 1-2 | CI basics: GitHub Actions / GitLab CI | Simple CI pipeline cho dbt |
| 3-4 | dbt CI: slim CI, deferred runs, PR validation | CI chạy trên mỗi PR |
| 5-6 | Pre-commit hooks: sqlfluff, yaml lint | Automated code quality |
| 7-8 | Deployment: dev → staging → production | Production deployment workflow |

**Key shift:** DA manually runs queries → AE has automated, reliable deployment

**Resources:**
- [GitHub Actions docs](https://docs.github.com/en/actions)
- [dbt Cloud CI docs](https://docs.getdbt.com/docs/deploy/continuous-integration)
- [Pre-commit for dbt](https://github.com/dbt-checkpoint/dbt-checkpoint)

### Month 11-12: Production & Orchestration

**Mục tiêu:** Own production analytics pipeline

| Tuần | Chủ đề | Practice |
|---|---|---|
| 1-2 | Airflow basics: DAGs, operators, scheduling | dbt DAG in Airflow |
| 3-4 | Monitoring: dbt run alerts, freshness, SLA | Production monitoring |
| 5-6 | Incident response: debug failed runs, backfill | Runbook creation |
| 7-8 | Semantic layer intro: metrics, definitions | MetricFlow exploration |

**Resources:**
- [Astronomer Academy (free)](https://academy.astronomer.io/)
- [Cosmos: dbt + Airflow](https://github.com/astronomer/astronomer-cosmos)

---

## 3. Mindset Shifts (quan trọng!)

> Đây là phần quan trọng nhất của cả roadmap này. Tool học vài tháng là xong — nhưng mindset shift cần thời gian và intentional practice. DA nghĩ "query chạy đúng là xong", AE nghĩ "model chạy đúng mọi lúc, cho mọi input, được test, được document." Sự khác biệt nằm ở chữ "systematic." Khi bạn shift được mindset — tool chỉ là chi tiết.

### Từ Ad-hoc → Systematic

| DA mindset | AE mindset |
|---|---|
| "Query chạy ra đúng kết quả" | "Model chạy đúng mọi lúc, cho mọi input" |
| "File .sql trong folder local" | "Code trong Git, reviewed, tested, deployed" |
| "Tôi biết data đúng" | "Tests chứng minh data đúng" |
| "Fix nhanh trên prod" | "Fix trên dev → test → deploy" |
| "Comment giải thích logic" | "Code tự giải thích + docs auto-generated" |

### Từ Consumer → Producer

| DA mindset | AE mindset |
|---|---|
| "Dashboard cần data gì?" | "Model serve được bao nhiêu use cases?" |
| "Query nhanh cho tôi" | "Model perform tốt cho 100 users" |
| "Data source có vấn đề, escalate" | "Data source có vấn đề, tôi handle trong code" |
| "Analysis xong, chuyển sang task khác" | "Model deployed, tôi monitor ongoing" |

### Từ Individual → System Thinker

| DA mindset | AE mindset |
|---|---|
| "Query riêng cho mỗi request" | "Reusable model cho nhiều consumers" |
| "Logic trong dashboard" | "Logic trong transformation layer (single source of truth)" |
| "Performance OK cho tôi" | "Performance OK cho production scale" |
| "Tôi hiểu data này" | "Documentation + lineage cho team hiểu" |

---

## 4. Practical Transition Strategy

> Có 3 con đường, mỗi đường phù hợp một hoàn cảnh khác nhau. "Gradualist" cho bạn đang full-time DA nhưng muốn từ từ chuyển. "Deep Dive" cho bạn sẵn sàng dành 3-4 tháng all-in. "Internal Transfer" là con đường ngắn nhất nếu company bạn đã có AE team. Chọn cái khớp với situation — đừng copy người khác.

### Strategy A: "Gradualist" (khuyến nghị nếu đang fulltime DA)

**Timeline:** 6-9 tháng part-time, sau đó chuyển role

1. **Tháng 1-3:** Tự học dbt + Git trong giờ rảnh. Migrate 3-5 existing queries thành dbt models
2. **Tháng 4-6:** Propose "pilot" dbt project cho 1 use case nhỏ trong team. Viết tests, docs
3. **Tháng 7-9:** Scale dbt project. Chứng minh value: "tôi reduce data issues by X%"
4. **Tháng 10+:** Formal role change hoặc chuyển team

### Strategy B: "Deep Dive" (nếu có thời gian fulltime)

**Timeline:** 3-4 tháng intensive

1. **Tháng 1:** dbt Fundamentals + Advanced courses. Build portfolio project
2. **Tháng 2:** Contribute to open-source dbt package hoặc build custom package
3. **Tháng 3:** Full project: sources → staging → marts → tests → CI/CD → docs
4. **Tháng 4:** Interview prep + apply

### Strategy C: "Internal Transfer" (tốt nhất nếu company có AE team)

1. Nói chuyện với AE team lead, express interest
2. Xin assign 1-2 dbt PRs/tasks
3. Shadow AE team trong 1-2 sprints
4. Formal transition request với evidence of capability

---

## 5. Portfolio để chứng minh transition

> Khi chuyển career, lời nói không đủ — bạn cần bằng chứng. Một GitHub repo với 15+ dbt models, tests xanh, CI pipeline chạy, docs generate đẹp — đó là "lời nói" mạnh nhất. Không cần phức tạp, cần chỉn chu. Hiring manager đọc README 30 giây là biết bạn có "engineer mindset" hay không.

### Must-have portfolio items:

1. **dbt Project trên GitHub** (public)
 - Ít nhất 15-20 models (staging + marts)
 - Tests coverage > 80%
 - Full documentation (auto-generated + custom)
 - CI pipeline (GitHub Actions)
 - README giải thích architecture decisions

2. **Blog post / Write-up**
 - "How I transitioned from DA to AE"
 - Technical post về modeling decision
 - dbt tip/trick bạn discovered

3. **dbt Certification**
 - [dbt Analytics Engineering Certification](https://www.getdbt.com/certifications/analytics-engineer-certification)
 - Strongest signal cho role transition

---

## 6. Interview Prep cho DA chuyển AE

> Interview cho career switcher thường bắt đầu bằng "Tại sao bạn muốn chuyển?" — và đây là câu bạn phải trả lời mượt nhất. Không phải "chán DA" mà là "muốn build hệ thống, muốn solve problems at scale." Phần technical thì focus vào dbt + data modeling — hai thứ bạn phải đủ confident để live-code.

### Câu hỏi thường gặp:

**Về transition:**
- "Tại sao muốn chuyển từ DA sang AE?" → Focus vào systematic thinking, scalability
- "DA experience giúp gì cho AE role?" → Business context, user empathy, data intuition
- "Bạn thiếu gì so với traditional AE?" → Acknowledge, show learning plan

**Technical:**
- Design dbt project structure cho use case cụ thể
- Viết incremental model với late-arriving data handling
- Debug failed dbt run (common errors)
- Explain ref() vs source() vs this()
- SCD Type 2 implementation

**Behavioral:**
- "Khi có data quality issue, bạn xử lý thế nào?" → Show systematic approach
- "Khi DA team request new model, process thế nào?" → Requirements → design → implement → test → deploy

### Red flags to avoid:
- ❌ "Tôi muốn chuyển vì chán DA" → ✅ "Tôi muốn build systems, không chỉ consume"
- ❌ "dbt dễ, chỉ là SQL" → ✅ "dbt discipline changes how I think about data"
- ❌ Không mention testing/quality → ✅ Testing first mindset

---

## 7. Common Challenges & Solutions

> Mọi người chuyển career đều gặp obstacles — bạn không phải ngoại lệ. "Không có access production", "Company không dùng dbt", "Git scares me" — tất cả đều có solution. Quan trọng là đừng để obstacle trở thành excuse. Dưới đây là real problems và cách người đi trước đã vượt qua.

| Challenge | Solution |
|---|---|
| "Tôi không có access vào production" | Use dbt Cloud IDE (free tier) + public datasets |
| "Company không dùng dbt" | Build personal project, propose pilot |
| "Không có mentor AE" | dbt Community Slack, Locally Optimistic |
| "Git/CLI scares me" | Start small: commit daily, GUI first (GitHub Desktop) |
| "Quá nhiều thứ phải học" | Focus dbt first, everything else supports it |
| "Interviewer thinks I'm too junior" | Portfolio + certification = proof |
| "Salary cut khi chuyển role?" | Unlikely — AE pays more. Even lateral = growth potential |

---

## 8. Checklist: Am I Ready?

> Đây là "fitness test" trước khi apply. Không cần tick hết 100% — nhưng nếu bạn tick được 7-8 items trong "minimum viable", bạn sẵn sàng. Đừng chờ hoàn hảo. Apply sớm, fail fast, learn faster. Mỗi interview là practice — ngay cả khi bạn không pass.

### Minimum viable AE (ready to apply):

- [ ] Comfortable với Git CLI (branch, commit, push, PR, merge)
- [ ] Completed dbt Fundamentals + Advanced course
- [ ] Built 1 dbt project (15+ models, tested, documented)
- [ ] Understand Kimball dimensional modeling
- [ ] Can write incremental models
- [ ] Have CI/CD pipeline running
- [ ] Understand data quality testing patterns
- [ ] Can read & write dbt macros (basic Jinja)
- [ ] Portfolio on GitHub
- [ ] dbt Analytics Engineering Certification (strongly recommended)

### Stretch (competitive advantage):

- [ ] Contributed to open-source dbt package
- [ ] Built semantic layer (MetricFlow)
- [ ] Airflow orchestration experience
- [ ] Cloud DW optimization (Redshift/Snowflake)
- [ ] Written technical blog posts
- [ ] Led internal dbt adoption initiative

---

## 9. Salary Comparison: DA vs AE (VN 2025-2026)

> Spoiler: AE trả nhiều hơn DA ở mọi level. Premium 20-40% là real — do supply thấp và technical bar cao hơn. Ngay cả khi bạn lateral move (cùng salary), growth potential AE cao hơn đáng kể trong 1-2 năm tiếp theo. Đầu tư 6 tháng transition = ROI rõ ràng trong salary và career options.

| Level | DA Salary | AE Salary | Premium |
|---|---|---|---|
| Junior | 12-22 triệu | 15-30 triệu | +20-35% |
| Mid | 20-40 triệu | 28-55 triệu | +30-40% |
| Senior | 35-70 triệu | 45-90 triệu | +25-30% |

> *AE premium do: lower supply, higher technical bar, direct infrastructure impact. Banking/fintech pays highest.*

---

## 11. Day-in-the-Life: Trước vs Sau transition

> Không gì minh họa transition tốt hơn so sánh "trước vs sau." Cùng một người, cùng đam mê data — nhưng cách làm việc hoàn toàn khác. Từ "copy logic từ report cũ" sang "single source of truth, tested, deployed, monitored." Đó là sức mạnh của engineering discipline. Và cảm giác khi CI xanh, model deploy production, DA dùng data bạn model — rất khó diễn tả.

### Trước (DA) — Typical day

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:30 | Check dashboard, note số liệu bất thường | Power BI |
| 9:00 | Mở DBeaver, viết query ad-hoc | SQL |
| 10:00 | PM hỏi: "Revenue hôm qua sao giảm?" | Slack |
| 10:30 | Deep-dive query, tìm root cause | SQL, Excel |
| 11:30 | Viết summary + chart, gửi PM | PowerPoint |
| 13:00 | Build new report theo request | Power BI |
| 15:00 | Copy logic từ report cũ, modify | SQL file local |
| 16:30 | Gửi output, done | Email |

**Pain points:**
- ❌ Query logic scattered across files, không version control
- ❌ Cùng 1 metric nhưng 3 người query khác nhau → inconsistent
- ❌ Data quality issue phát hiện muộn (từ business user)
- ❌ "Run lại query này mỗi sáng" → manual, error-prone

### Sau (AE) — Typical day

| Thời gian | Hoạt động | Tools |
|---|---|---|
| 8:30 | Check dbt run results + data quality alerts | Airflow, Slack |
| 9:00 | Standup: "PR cho customer mart ready for review" | Teams |
| 9:30 | Code: refactor revenue logic → single source of truth | VS Code, dbt |
| 10:30 | Write test: `assert revenue > 0`, freshness < 2h | YAML |
| 11:00 | PR review từ teammate | GitHub |
| 13:00 | Meeting với DA: "Bạn cần metrics gì? Tôi model sẵn" | Teams |
| 14:00 | Design intermediate model, write docs | dbt, dbdiagram.io |
| 15:30 | CI chạy xong, merge to main, auto-deploy staging | GitHub Actions |
| 16:00 | Monitor: check model performance on staging | Redshift |
| 16:30 | Update docs, close Jira ticket | dbt docs, Jira |

**Gains:**
- ✅ Single source of truth → mọi người dùng cùng 1 metric
- ✅ Tests chạy mỗi ngày → issue caught sớm
- ✅ Version control → biết ai thay đổi gì, khi nào
- ✅ Documentation tự generate → team tự phục vụ
- ✅ CI/CD → deploy tự tin, rollback dễ

---

## 12. Vietnam Job Market: DA → AE transition

> VN market đang ở điểm "rất thuận lợi" cho DA → AE transition. Nhiều companies đang build AE team, prefer candidates có business context (từ DA) hơn pure engineers. Nếu bạn đã hiểu banking/fintech domain + thêm dbt skills, bạn là "unicorn" mà market đang tìm. Đừng đợi — timing is now.

### Companies VN đang build AE team (2025-2026)

| Company | Context | Transition-friendly? |
|---|---|---|
| MoMo | Data team growing, dbt adoption | ✅ If you know dbt |
| Infinite Lambda VN | Hire remote AE, train internally | ✅ Good entry point |
| Holistics | Analytics product company | ✅ dbt + analytics focus |
| Grab VN | Large data team, structured | ⚠️ Competitive |
| Techcombank | Modern data stack initiative | ✅ Banking context helps |
| VIB | Data platform modernization | ✅ Internal move possible |

### Salary shift khi transition

| Scenario | Before (DA) | After (AE) | Timeline |
|---|---|---|---|
| Junior DA → Junior AE | 15-20tr | 18-28tr | +20-40%, 6-12 months |
| Mid DA → Junior AE (new role) | 25-35tr | 22-30tr | Slight dip initially |
| Mid DA → Mid AE (proven) | 25-35tr | 35-50tr | +30-40%, 12+ months |
| Senior DA → Mid AE | 40-60tr | 40-55tr | Lateral, growth potential |

> **Note:** Salary dip khi switch rất hiếm nếu bạn đã có portfolio + cert. Hầu hết cases = salary increase.

### JD keywords to look for (DA → AE transition-friendly):

- "SQL + dbt" (explicit AE)
- "Data modeling + testing" (AE work, any title)
- "Analytics Engineer" (obvious)
- "BI Engineer" (often = AE work tại VN banking)
- "Data Analyst - transformation" (AE in disguise)

---

## 13. Learning Schedule cho Transition

> 6 tháng, 17h/tuần — nghe như nhiều nhưng thực ra chỉ là 2h tối + 3h weekend. Bạn vẫn đi làm DA bình thường, vẫn có cuộc sống. Key là consistency: mỗi tối mở laptop 2h, mỗi sáng commute đọc 1 blog post. 6 tháng sau bạn sẽ không nhận ra mình đã đi xa đến đâu.

### Template: Part-time while working as DA (2h/ngày)

**24 tuần (6 tháng) từ DA → interview-ready AE**

| Giai đoạn | Tuần | Weekday (2h/ngày) | Weekend (3h) | Milestone |
|---|---|---|---|---|
| Git + CLI | 1-3 | Git exercises, CLI practice | Setup repo, first PR | ✅ Comfortable with Git |
| dbt Basics | 4-7 | dbt Fundamentals course | Jaffle Shop project | ✅ dbt Learn cert |
| First Project | 8-11 | Migrate DA queries → dbt models | Build staging + marts | ✅ 10 models on GitHub |
| Testing + Docs | 12-14 | Tests + documentation | Full test coverage | ✅ Quality framework |
| Incremental | 15-17 | Incremental patterns | Convert to incremental | ✅ Production patterns |
| CI/CD | 18-20 | GitHub Actions + sqlfluff | Automated pipeline | ✅ Full CI/CD |
| Portfolio | 21-22 | Polish, README, diagrams | Record demo | ✅ Presentable project |
| Interview | 23-24 | Cert prep + mock interviews | Exam day | ✅ dbt Certified + ready |

### Daily routine suggestion (đang fulltime DA):

| Time | Activity |
|---|---|
| 7:00-7:30 | Morning: read 1 dbt blog post (commute) |
| Lunch 12:00-12:30 | Solve 1 SQL challenge (StrataScratch) |
| 20:00-22:00 | Evening: deep learning + practice |
| Weekend 9:00-12:00 | Project work (3h focused) |

**Total: ~17h/tuần** — Đủ để transition trong 6 tháng

---

> *Bạn đã đọc đến đây — nghĩa là bạn nghiêm túc. Đó đã là bước đầu tiên quan trọng nhất. 6 tháng từ giờ, bạn có thể là người deploy dbt model đầu tiên của team, là người mà DA tìm đến khi cần data, là người mà manager tin tưởng giao ownership. Hành trình từ DA sang AE không phải nhảy vực — mà là leo cầu thang. Mỗi bước nhỏ đều đếm. Bắt đầu hôm nay.*
