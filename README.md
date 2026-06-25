# Lộ trình Phát triển Sự nghiệp ngành Data theo Vai trò (Data Career Roadmaps by Roles)

Chào mừng bạn đến với kho lưu trữ **Data Career Roadmaps**. Đây là bộ cẩm nang chi tiết, lộ trình học tập và làm việc được thiết kế nhằm hỗ trợ các thành viên trong đội ngũ Data định hướng, nâng cao năng lực và phát triển sự nghiệp theo từng vai trò chuyên biệt.

---

## 🗺️ Bản đồ các Lộ trình (Roadmaps Overview)

Repository này cung cấp 3 lộ trình chi tiết tương ứng với các cột mốc sự nghiệp khác nhau:

### 1. [Data Analyst (DA)](./ROADMAP-DA.md)
* **Đối tượng:** Thành viên mới bắt đầu hoặc đang làm việc ở vị trí Data Analyst muốn củng cố nền tảng để tự chủ hoàn toàn trong việc giải quyết yêu cầu từ business.
* **Thời gian:** 6 tháng (từ Junior lên Mid/Senior DA).
* **Nội dung trọng tâm:**
  * **Phase 1 (Tuần 1-4):** SQL Foundation (CTEs, Window Functions, Window Frame).
  * **Phase 2 (Tuần 5-8):** Data Exploration & Profiling (Khám phá Schema, kiểm soát chất lượng dữ liệu).
  * **Phase 3 (Tuần 9-12):** Business Domain Knowledge (E-commerce, SaaS, Banking & Fintech).
  * **Phase 4 (Tuần 13-16):** Python cho Data Analysis (Pandas, Seaborn, Thống kê cơ bản).
  * **Phase 5 (Tuần 17-20):** Reporting & Communication (Kể chuyện qua dữ liệu, quản lý kỳ vọng của stakeholder).
  * **Phase 6 (Tuần 21-24):** Advanced & Specialization (Tối ưu hóa Query, A/B Testing).

### 2. [BI Engineer (BIE)](./ROADMAP-BI-ENGINEER.md)
* **Đối tượng:** Các thành viên đã vững SQL, hiểu nghiệp vụ doanh nghiệp (DA) và muốn chuyển dịch sang hướng tối ưu hóa báo cáo, thiết kế semantic layer và thúc đẩy self-service analytics.
* **Thời gian:** 5 - 12 tháng.
* **Nội dung trọng tâm:**
  * **Phase 1 (Tuần 1-4):** BI Tool Mastery (Apache Superset, Amazon QuickSight).
  * **Phase 2 (Tuần 5-10):** Dimensional Modeling (Thiết kế Star Schema, Snowflake, SCD Type 2).
  * **Phase 3 (Tuần 11-14):** Data Visualization Best Practices (Quy tắc thiết kế 5 giây, phối màu chuẩn).
  * **Phase 4 (Tuần 15-18):** KPI & Semantic Layer (Định nghĩa KPIs dùng chung toàn doanh nghiệp).
  * **Phase 5 (Tuần 19-22):** Performance & Self-Service (Materialized views, aggregate tables, phân quyền).
  * **Phase 6 (Tuần 23+):** BI Governance & Ops (Quy trình vận hành, audit và thu hồi dashboard cũ).

### 3. [Data Analyst → Analytics Engineer (DA to AE)](./ROADMAP-DA-TO-AE.md)
* **Đối tượng:** Data Analyst hoặc BI Engineer muốn chuyển mình sang vai trò Analytics Engineer (AE) — người chịu trách nhiệm chính ở Transformation Layer (dbt), áp dụng các nghệ thuật phần mềm (Software Engineering) vào dữ liệu.
* **Thời gian:** 12 - 18 tháng.
* **Nội dung trọng tâm:**
  * **Phase 1-2 (Tháng 1-6):** Xây dựng nền tảng vững chắc về SQL, Business và thiết lập Dashboard.
  * **Phase 3 (Tháng 7-12):** Chuyển giao sang AE (Làm chủ dbt, Git workflow, CI/CD, dbt test).
  * **Phase 4 (Tháng 12+):** SWE Best Practices (Quản trị môi trường SIT/UAT/PROD, Testing Pyramid, Data Governance, Security, Observability).

---

## 🌐 Triết lý Thiết kế: Đa Doanh nghiệp & Đa Lĩnh vực (Multi-Domain Approach)

Một trong những cải tiến cốt lõi của bộ tài liệu này là **tính thích ứng cao với nhiều domain khác nhau** thay vì chỉ đóng khung trong một lĩnh vực duy nhất. 

Trong mỗi roadmap, các ví dụ thực hành, thiết kế Data Model (Star Schema) và danh sách KPI đều được phân bổ cân bằng và chi tiết qua 3 ngành công nghiệp phổ biến nhất hiện nay:
1. 🛒 **E-commerce & Retail (Thương mại điện tử & Bán lẻ):** Tập trung vào luồng bán hàng (Sales, Orders), Phân khúc khách hàng (Customer cohorts), Vận chuyển và Tiếp thị.
2. 💻 **SaaS & Subscription (Phần mềm dịch vụ):** Chú trọng vào doanh thu định kỳ (MRR/ARR), chi phí thu hút khách hàng (CAC), giá trị vòng đời (LTV) và tỷ lệ rời bỏ (Churn Rate).
3. 🏦 **Banking & Fintech (Tài chính & Ngân hàng):** Đi sâu vào nghiệp vụ tín dụng (Credit/Lending), Nợ xấu (NPL), Quản lý số dư (CASA Ratio), và Biên thu nhập lãi thuần (NIM).

Nhờ cách thiết kế này, dù doanh nghiệp của bạn hoạt động ở bất kỳ lĩnh vực nào, bạn đều có thể dễ dàng đối chiếu các khái niệm trong lộ trình vào bài toán thực tế của công ty.

---

## 🛠️ Hướng dẫn Sử dụng (How to Use)

1. **Xác định Vị trí Hiện tại:** Đọc kỹ mô tả nhiệm vụ và bảng đối chiếu kỹ năng (Skill Matrix) ở cuối mỗi roadmap để định vị bản thân đang ở mức độ nào.
2. **Theo sát các Deliverables:** Mỗi phase đều đi kèm với checklist công việc (`[ ] Deliverables`). Hãy đảm bảo hoàn thành và được Review trước khi bước sang phase tiếp theo.
3. **Thực hành Thực tế:** Tận dụng các template có sẵn (như *KPI Documentation Template*, *Profiling Template*, *SWE Environments structure*) để áp dụng ngay vào công việc hàng ngày tại dự án của bạn.
4. **Tham khảo Tài nguyên:** Ở cuối mỗi file đều có danh sách tài liệu đọc thêm (Resources) từ các nguồn uy tín hàng đầu thế giới để bạn tự nghiên cứu sâu hơn.

---

*Chúc các bạn có một hành trình phát triển sự nghiệp rực rỡ và đóng góp nhiều giá trị dữ liệu thiết thực cho doanh nghiệp!*
