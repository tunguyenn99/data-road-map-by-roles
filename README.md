# 🗺️ Data Career Roadmaps — Mục lục

> *Nếu bạn đang đọc trang này, có thể bạn đang ở một trong những ngã rẽ sau: vừa tốt nghiệp và tự hỏi "data career bắt đầu từ đâu?", đang làm DA nhưng muốn growth mạnh hơn, hoặc đang ở ngành khác mà nghe nói "data is the new oil" quá nhiều lần. Dù bạn ở đâu — bạn đến đúng chỗ rồi. Bộ roadmaps này không phải lý thuyết suông — mà là bản đồ thực tế, viết từ góc nhìn của người đã đi qua và đang đi cùng bạn.*

## Tổng quan

Bộ 6 Career Roadmaps cho các vai trò trong Data team, thiết kế với 5 levels:
**Intern → Fresher → Junior → Mid → Senior**

Mỗi roadmap bao gồm:
- Tổng quan vai trò & responsibilities
- Skill matrix theo level (Technical + Soft skills)
- Learning path chi tiết theo phase
- Resources verified (courses, books, certifications, communities)
- Portfolio projects gợi ý
- Interview prep
- Salary benchmark (VN market 2025-2026)
- Career progression paths
- **Day-in-the-Life stories** (theo level: Intern → Senior)
- **Vietnam Job Market Analysis** (companies, JD mẫu, hiring trends)
- **Learning Schedule Templates** (part-time 2h/ngày & full-time intensive)

---

## 📚 Danh sách Roadmaps

Mỗi role trong data giống như một nghề thủ công riêng — cùng làm việc với dữ liệu, nhưng góc nhìn, công cụ, và "sản phẩm cuối" hoàn toàn khác nhau. DA giống thám tử tìm insight ẩn trong data. BI Developer là kiến trúc sư hệ thống thông tin cho cả tổ chức. BI Engineer là người xây nền móng phía sau mỗi dashboard đẹp. Data Governance là "hệ miễn dịch" giữ cho data organization khỏe mạnh. Analytics Engineer là nhà máy lọc — biến quặng thô thành vàng ròng, có quy trình, có kiểm định.

| # | Role | File | Mô tả |
|---|---|---|---|
| 1 | **Data Analyst (DA)** | [ROADMAP-DA.md](./ROADMAP-DA.md) | Phân tích dữ liệu, insight, storytelling — thám tử của thế giới data |
| 2 | **Business Intelligence (BI)** | [ROADMAP-BI.md](./ROADMAP-BI.md) | Dashboard, reporting systems, visualization — kiến trúc sư thông tin |
| 3 | **BI Engineer** | [ROADMAP-BI-ENGINEER.md](./ROADMAP-BI-ENGINEER.md) | BI infrastructure, pipelines, data warehouse — người xây nền móng |
| 4 | **Data Governance (DG)** | [ROADMAP-DG.md](./ROADMAP-DG.md) | Policies, quality, compliance, catalog — hệ miễn dịch của data org |
| 5 | **Analytics Engineer (AE)** | [ROADMAP-AE.md](./ROADMAP-AE.md) | dbt, transformation layer, data modeling — nhà máy lọc dữ liệu |
| 6 | **Switch: DA → AE** | [ROADMAP-SWITCH-DA-TO-AE.md](./ROADMAP-SWITCH-DA-TO-AE.md) | Transition guide, timeline, portfolio — con đường tự nhiên nhất |

---

## 🔀 So sánh nhanh các roles

Hãy tưởng tượng data team như một nhà hàng: DA là người nếm thử và review món ăn (insight), BI Developer là người bày trí và serve (dashboard), BI Engineer là đầu bếp phụ chuẩn bị nguyên liệu sạch (pipeline), AE là người chuẩn hóa công thức nấu (transformation), và DG là người đảm bảo vệ sinh an toàn thực phẩm (governance). Mỗi role thiếu một thì nhà hàng không thể vận hành.

### Technical Focus

```
                    Business ◄─────────────────────────────► Engineering
                         │                                       │
               DA ───────┼──── BI ──── DG ──── AE ──── BI Eng ──┤
                         │                                       │
            Insight      │    Report   Policy  Transform  Infra  │
```

### Skill Overlap

| Skill | DA | BI | BI Eng | DG | AE |
|---|:---:|:---:|:---:|:---:|:---:|
| SQL | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| Python | ⭐⭐ | ⭐ | ⭐⭐⭐ | ⭐ | ⭐⭐ |
| Statistics | ⭐⭐⭐ | ⭐ | ⭐ | ⭐ | ⭐ |
| BI Tools | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ⭐ | ⭐ |
| Data Modeling | ⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| dbt | - | - | ⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐ |
| Git/CI/CD | ⭐ | ⭐ | ⭐⭐⭐ | ⭐ | ⭐⭐⭐ |
| Cloud DW | ⭐ | ⭐ | ⭐⭐⭐ | ⭐ | ⭐⭐⭐ |
| Policy/Compliance | - | ⭐ | ⭐ | ⭐⭐⭐⭐ | ⭐ |
| Business Acumen | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐ |

### Salary Ranges (Mid level, VND/tháng)

| Role | Range | Demand trend |
|---|---|---|
| DA | 20-40tr | Stable, high supply |
| BI | 22-45tr | Stable, moderate supply |
| BI Engineer | 25-50tr | Growing fast |
| DG | 22-45tr | Growing (regulation-driven) |
| AE | 28-55tr | Highest growth, lowest supply |

---

## 🎯 Chọn role nào?

Nếu bạn đang phân vân — đừng lo, đó là bình thường. Không ai bắt đầu career bằng cách biết chính xác mình muốn gì. Flowchart dưới đây giúp bạn nhìn rõ hơn thiên hướng của mình. Nhưng nhớ: career paths không linear — nhiều người bắt đầu DA, rẽ sang AE, rồi quay lại BI. Quan trọng là bạn bắt đầu đi.

### Flowchart quyết định

```
Bạn thích gì nhất?
│
├── "Tìm insight, kể story bằng data"
│   └── → Data Analyst
│
├── "Xây dashboard đẹp, serve cả company"
│   └── → BI Analyst/Developer
│
├── "Build hệ thống data pipeline + BI"
│   └── → BI Engineer
│
├── "Đảm bảo data đúng, an toàn, tuân thủ"
│   └── → Data Governance
│
├── "Write code biến raw → trusted data, apply SWE practices"
│   └── → Analytics Engineer
│
└── "Đang là DA, muốn upskill + salary growth"
    └── → Switch DA → AE
```

---

## 📅 Last Updated

June 2026

---

## 📝 Notes

- Salary benchmarks dựa trên market VN (HCM/HN), khảo sát từ TopDev, ITViec, LinkedIn, Glassdoor
- Resources links verified tại thời điểm viết (June 2026)
- Career paths không linear — có thể combine skills từ nhiều roles
- Context đã được thêm vào các roadmap relevant (DG, AE)

---

> *Dù bạn chọn con đường nào — nhớ rằng mọi senior đều từng là fresher lo lắng. Mọi expert đều từng Google "what is LEFT JOIN." Bạn không cần giỏi sẵn — bạn chỉ cần bắt đầu và không dừng lại. Data career tại Việt Nam đang ở thời điểm vàng: demand tăng, salary tăng, cơ hội remote tăng. Hãy tận dụng momentum này. Chúc bạn tìm được con đường phù hợp, và enjoy hành trình.*
