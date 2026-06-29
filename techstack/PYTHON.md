# 🐍 Python — Learning Guide (Beginner → Advanced)

## Tổng quan

Python là ngôn ngữ lập trình phổ biến nhất trong data. Dùng cho: data manipulation, automation, ETL scripting, statistical analysis, machine learning.

**Dùng cho:** DA (Pandas, viz), AE (scripting, dbt packages), BI Eng (ETL, APIs), DG (automation)

---

## Level 1: Beginner (3 tuần)

### Core Concepts
- Variables, data types (str, int, float, bool, list, dict, tuple, set)
- Control flow: if/elif/else, for, while
- Functions: def, parameters, return
- File I/O: read/write files
- String manipulation
- List comprehensions
- Error handling: try/except

### Code Examples
```python
# Variables & types
name = "DataCompany"
revenue = 1_500_000
is_active = True
customers = ["A", "B", "C"]
config = {"host": "redshift.aws", "port": 5439}

# Functions
def calculate_growth(current: float, previous: float) -> float:
    """Calculate period-over-period growth rate."""
    if previous == 0:
        return 0
    return (current - previous) / previous * 100

# List comprehension
active_users = [u for u in users if u["status"] == "active"]

# File reading
with open("data.csv", "r") as f:
    lines = f.readlines()

# Error handling
try:
    result = revenue / transactions
except ZeroDivisionError:
    result = 0
```

### Bài tập (15 bài)
1. Read CSV, calculate average of a column
2. Filter list of dicts by condition
3. Write function to clean phone numbers
4. Count word frequency in text file
5. Simple CLI tool: input → process → output

### Resources
| Resource | Link | Type |
|---|---|---|
| Python.org Tutorial | [docs.python.org/3/tutorial](https://docs.python.org/3/tutorial/) | Free |
| Automate the Boring Stuff | [automatetheboringstuff.com](https://automatetheboringstuff.com/) | Free |
| Python for Everybody (Coursera) | [coursera.org](https://www.coursera.org/specializations/python) | Free audit |
| Codecademy Python | [codecademy.com](https://www.codecademy.com/learn/learn-python-3) | Free/Paid |
| Real Python | [realpython.com](https://realpython.com/) | Free/Paid |

---

## Level 2: Intermediate (6 tuần)

### Data Analysis Stack
```python
# Pandas — Data manipulation
import pandas as pd

df = pd.read_csv("transactions.csv")
df["date"] = pd.to_datetime(df["date"])
df["month"] = df["date"].dt.to_period("M")

# Aggregation
monthly = df.groupby("month").agg(
    revenue=("amount", "sum"),
    orders=("order_id", "nunique"),
    aov=("amount", "mean")
).reset_index()

# Filtering
high_value = df[df["amount"] > 1_000_000]
recent = df[df["date"] >= "2024-01-01"]

# Merge (SQL JOIN equivalent)
result = pd.merge(customers, orders, on="customer_id", how="left")

# Pivot table
pivot = df.pivot_table(
    values="amount",
    index="region",
    columns="product",
    aggfunc="sum"
)
```

### Visualization
```python
# Matplotlib + Seaborn
import matplotlib.pyplot as plt
import seaborn as sns

fig, ax = plt.subplots(figsize=(10, 6))
sns.lineplot(data=monthly, x="month", y="revenue", ax=ax)
ax.set_title("Monthly Revenue Trend")
plt.savefig("revenue_trend.png", dpi=150, bbox_inches="tight")

# Plotly (interactive)
import plotly.express as px
fig = px.bar(monthly, x="month", y="revenue", color="region")
fig.show()
```

### API & Database Connection
```python
# Database connection (Redshift)
import sqlalchemy

engine = sqlalchemy.create_engine(
    "redshift+redshift_connector://user:pass@host:5439/db"
)
df = pd.read_sql("SELECT * FROM golden.customer LIMIT 100", engine)

# REST API
import requests

response = requests.get("https://api.example.com/data", headers={"Authorization": "Bearer token"})
data = response.json()
```

### Bài tập
1. EDA notebook: load dataset, clean, visualize, write findings
2. Automate daily report: query DB → process → export Excel
3. Build simple ETL: API → transform → CSV
4. Create reusable data validation functions

### Resources
| Resource | Link | Type |
|---|---|---|
| Python for Data Analysis (book) | Wes McKinney (3rd Ed) | Paid |
| Pandas docs | [pandas.pydata.org](https://pandas.pydata.org/docs/) | Free |
| DataCamp Python tracks | [datacamp.com](https://www.datacamp.com/tracks/data-analyst-with-python) | Paid |
| Kaggle Learn Python | [kaggle.com/learn/python](https://www.kaggle.com/learn/python) | Free |
| 10 Minutes to Pandas | [pandas.pydata.org/docs/user_guide/10min.html](https://pandas.pydata.org/docs/user_guide/10min.html) | Free |

---

## Level 3: Advanced (6 tuần)

### Software Engineering Practices
```python
# Type hints
from typing import Optional
from datetime import date

def get_business_date(override: Optional[date] = None) -> date:
    return override or date.today()

# Pydantic (data validation)
from pydantic import BaseModel, ConfigDict

class ModelConfig(BaseModel):
    model_config = ConfigDict(frozen=True)
    
    schema_name: str
    table_name: str
    partition_column: str = "partition_date"

# Dataclasses
from dataclasses import dataclass

@dataclass
class QueryResult:
    rows: int
    execution_time_ms: float
    data: list[dict]
```

### Testing
```python
# pytest
import pytest
from my_module import calculate_growth

def test_calculate_growth_normal():
    assert calculate_growth(120, 100) == 20.0

def test_calculate_growth_zero_previous():
    assert calculate_growth(100, 0) == 0

@pytest.fixture
def sample_df():
    return pd.DataFrame({"amount": [100, 200, 300]})

def test_total(sample_df):
    assert sample_df["amount"].sum() == 600
```

### Automation & Scripting
```python
# Scheduling with cron (conceptual)
# Or with Python schedule library
import schedule
import time

def daily_report():
    df = query_database()
    report = generate_report(df)
    send_email(report)

schedule.every().day.at("08:00").do(daily_report)

# CLI tool with argparse
import argparse

parser = argparse.ArgumentParser(description="Data quality check")
parser.add_argument("--table", required=True, help="Table to check")
parser.add_argument("--date", default="today", help="Business date")
args = parser.parse_args()
```

### Packages for Data Engineering
```python
# Great Expectations (data quality)
import great_expectations as gx

context = gx.get_context()
# Define expectations, validate data

# SQLAlchemy ORM
from sqlalchemy import Column, Integer, String, create_engine
from sqlalchemy.orm import declarative_base

Base = declarative_base()
class Customer(Base):
    __tablename__ = "customers"
    id = Column(Integer, primary_key=True)
    name = Column(String(100))

# Jinja2 (used in dbt)
from jinja2 import Template
template = Template("SELECT * FROM {{ schema }}.{{ table }}")
sql = template.render(schema="golden", table="customer")
```

### Bài tập
1. Build CLI tool: data quality checker for Redshift tables
2. Write pytest suite for data transformation functions
3. Create Python package with proper structure (src/, tests/, pyproject.toml)
4. Automation: scheduled Slack alerts for data freshness

### Resources
| Resource | Link | Type |
|---|---|---|
| Fluent Python (book) | Luciano Ramalho | Paid |
| Effective Python (book) | Brett Slatkin | Paid |
| pytest docs | [docs.pytest.org](https://docs.pytest.org/) | Free |
| Real Python Advanced | [realpython.com](https://realpython.com/tutorials/advanced/) | Free/Paid |
| Python Packaging Guide | [packaging.python.org](https://packaging.python.org/) | Free |

---
## Alternatives & Công cụ tương đương

| Category | Python | Alternatives |
|---|---|---|
| Data manipulation | Pandas, Polars | R (dplyr), Julia, Spark |
| Visualization | Matplotlib, Plotly | R (ggplot2), JavaScript (D3.js) |
| Automation/ETL | Python scripts | Bash, Go, Rust |
| Statistical | statsmodels, scipy | R (native), SAS, SPSS |
| ML/AI | scikit-learn, PyTorch | R (caret), Julia (Flux) |
