# 🗃️ SQL — Learning Guide (Beginner → Advanced)

## Tổng quan

SQL (Structured Query Language) là ngôn ngữ nền tảng cho mọi data role. Dùng để query, manipulate, và quản lý dữ liệu trong relational databases.

**Dùng cho:** DA, BI, AE, BI Eng, DG, DE — tất cả roles

---

## Level 1: Beginner (2 tuần)

### Concepts cần nắm
- Database, table, row, column
- Data types: INT, VARCHAR, DATE, BOOLEAN, NUMERIC
- NULL handling
- Primary key, Foreign key

### Syntax cơ bản
```sql
-- Select & Filter
SELECT column1, column2 FROM table WHERE condition;
SELECT * FROM customers WHERE age > 25;
SELECT name, email FROM users WHERE status = 'active' ORDER BY name;

-- Operators
WHERE price BETWEEN 10 AND 50;
WHERE name LIKE '%bank%';
WHERE status IN ('active', 'pending');
WHERE email IS NOT NULL;

-- Aggregate
SELECT COUNT(*), AVG(salary), MAX(age), MIN(age), SUM(revenue)
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;

-- Sorting & Limiting
ORDER BY created_at DESC
LIMIT 10 OFFSET 20;
```

### Bài tập (20 bài)
1. Select tất cả customers ở HCM
2. Count số orders theo tháng
3. Tìm top 5 products by revenue
4. Tìm customers chưa bao giờ mua hàng (NULL check)
5. Average order value theo region

### Resources
| Resource | Link | Type |
|---|---|---|
| SQLBolt (interactive) | [sqlbolt.com](https://sqlbolt.com/) | Free |
| W3Schools SQL | [w3schools.com/sql](https://www.w3schools.com/sql/) | Free |
| Khan Academy SQL | [khanacademy.org](https://www.khanacademy.org/computing/computer-programming/sql) | Free |
| Mode SQL Tutorial | [mode.com/sql-tutorial](https://mode.com/sql-tutorial/) | Free |

---

## Level 2: Intermediate (4 tuần)

### Concepts
- JOINs: INNER, LEFT, RIGHT, FULL OUTER, CROSS
- Subqueries (correlated & non-correlated)
- Common Table Expressions (CTEs)
- UNION, INTERSECT, EXCEPT
- CASE statements
- Date functions

### Key Patterns
```sql
-- JOINs
SELECT c.name, o.total
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id;

-- CTE
WITH monthly_revenue AS (
    SELECT DATE_TRUNC('month', order_date) AS month,
           SUM(amount) AS revenue
    FROM orders
    GROUP BY 1
)
SELECT month, revenue,
       LAG(revenue) OVER (ORDER BY month) AS prev_month
FROM monthly_revenue;

-- Subquery
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- CASE
SELECT name,
    CASE
        WHEN age < 25 THEN 'Young'
        WHEN age < 40 THEN 'Mid'
        ELSE 'Senior'
    END AS age_group
FROM customers;
```

### Window Functions (quan trọng!)
```sql
-- ROW_NUMBER, RANK, DENSE_RANK
SELECT name, department, salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees;

-- Running total
SELECT date, revenue,
    SUM(revenue) OVER (ORDER BY date ROWS UNBOUNDED PRECEDING) AS running_total
FROM daily_sales;

-- Moving average
SELECT date, revenue,
    AVG(revenue) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma_7d
FROM daily_sales;

-- LAG / LEAD
SELECT month, revenue,
    revenue - LAG(revenue) OVER (ORDER BY month) AS mom_change
FROM monthly_revenue;
```

### Bài tập (30 bài)
- StrataScratch: 20 Medium problems
- LeetCode Database: 10 Medium problems
- Focus: window functions, CTEs, complex JOINs

### Resources
| Resource | Link | Type |
|---|---|---|
| StrataScratch | [stratascratch.com](https://www.stratascratch.com/) | Free/Paid |
| LeetCode Database | [leetcode.com/problemset/database](https://leetcode.com/problemset/database/) | Free |
| Window Functions Guide | [postgresqltutorial.com/postgresql-window-function](https://www.postgresqltutorial.com/postgresql-window-function/) | Free |
| DataLemur SQL | [datalemur.com](https://datalemur.com/) | Free |

---

## Level 3: Advanced (4 tuần)

### Concepts
- Query optimization & EXPLAIN plans
- Indexing strategies
- Materialized views
- Recursive CTEs
- Pivoting / Unpivoting
- Database-specific features (Redshift, BigQuery, Snowflake)

### Performance Optimization
```sql
-- EXPLAIN plan
EXPLAIN ANALYZE
SELECT c.name, SUM(o.amount)
FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.created_at > '2024-01-01'
GROUP BY c.name;

-- Distribution key (Redshift-specific)
-- Check if tables are co-located
SELECT "table", diststyle, sortkey1
FROM svv_table_info
WHERE schema = 'golden';

-- Avoid SELECT *
-- Use specific columns to reduce I/O

-- Predicate pushdown
-- Filter early in CTEs, not late in final SELECT
```

### Advanced Patterns
```sql
-- Recursive CTE (org hierarchy)
WITH RECURSIVE org_tree AS (
    SELECT id, name, manager_id, 1 AS level
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.id, e.name, e.manager_id, ot.level + 1
    FROM employees e
    JOIN org_tree ot ON e.manager_id = ot.id
)
SELECT * FROM org_tree;

-- PIVOT (Redshift)
SELECT customer_id,
    MAX(CASE WHEN product = 'A' THEN amount END) AS product_a,
    MAX(CASE WHEN product = 'B' THEN amount END) AS product_b
FROM orders
GROUP BY customer_id;

-- Incremental pattern (dbt-style)
SELECT *
FROM source_table
WHERE updated_at > (SELECT MAX(updated_at) FROM target_table);
```

### Redshift-specific
```sql
-- Distribution styles
CREATE TABLE fact_transactions (
    id BIGINT,
    customer_id BIGINT,
    amount DECIMAL(18,2)
)
DISTSTYLE KEY
DISTKEY (customer_id)
SORTKEY (transaction_date);

-- Query performance
SELECT query, elapsed, substring
FROM svl_qlog
WHERE elapsed > 1000000 -- > 1 second
ORDER BY elapsed DESC LIMIT 20;

-- Table statistics
ANALYZE table_name;
VACUUM table_name;
```

### Bài tập
- Optimize 5 slow queries (use EXPLAIN)
- Write recursive CTE cho tree structure
- Design distribution keys cho 3 fact tables
- Benchmark: same query with different approaches

### Resources
| Resource | Link | Type |
|---|---|---|
| Use The Index, Luke | [use-the-index-luke.com](https://use-the-index-luke.com/) | Free |
| Redshift Best Practices | [AWS Docs](https://docs.aws.amazon.com/redshift/latest/dg/c_designing-tables-best-practices.html) | Free |
| Advanced SQL (DataCamp) | [datacamp.com](https://www.datacamp.com/tracks/sql-fundamentals) | Paid |
| SQL Performance Explained (book) | Markus Winand | Paid |

---
## Alternatives & Công cụ tương đương

| Approach | Tools | When to use |
|---|---|---|
| Traditional SQL | PostgreSQL, MySQL, SQL Server | On-prem, small-medium data |
| Cloud SQL | Redshift, BigQuery, Snowflake, Databricks SQL | Cloud analytics, large scale |
| NoSQL alternatives | MongoDB (MQL), DynamoDB | Unstructured/semi-structured data |
| DataFrame SQL | DuckDB, Polars, Spark SQL | Local analytics, embedded |
