# 🌬️ Apache Airflow — Learning Guide (Beginner → Advanced)

## Tổng quan

Apache Airflow là orchestration platform. Schedule, monitor, và quản lý data pipelines (DAGs). Airflow không xử lý data trực tiếp mà trigger và giám sát các tasks.

**Dùng cho:** AE, BI Eng, DE

---

## Level 1: Beginner (2 tuần)

### Core Concepts
- **DAG** (Directed Acyclic Graph): workflow definition
- **Task**: single unit of work
- **Operator**: predefined task template (BashOperator, PythonOperator, etc.)
- **Schedule**: cron expression hoặc interval
- **XCom**: pass data between tasks
- **Connection**: stored credentials

### First DAG
```python
from __future__ import annotations
from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator

default_args = {
    "owner": "data-team",
    "retries": 2,
    "retry_delay": timedelta(minutes=5),
}

with DAG(
    dag_id="my_first_dag",
    default_args=default_args,
    description="Simple tutorial DAG",
    schedule="0 8 * * *",  # Daily at 8am
    start_date=datetime(2024, 1, 1),
    catchup=False,
    tags=["tutorial"],
) as dag:

    hello = BashOperator(
        task_id="say_hello",
        bash_command="echo 'Hello Airflow!'",
    )

    def process_data():
        print("Processing data...")
        return "done"

    process = PythonOperator(
        task_id="process_data",
        python_callable=process_data,
    )

    hello >> process  # Task dependency
```

### Cron Schedules
```
┌─── minute (0-59)
│ ┌─── hour (0-23)
│ │ ┌─── day of month (1-31)
│ │ │ ┌─── month (1-12)
│ │ │ │ ┌─── day of week (0-6, Sun=0)
│ │ │ │ │
0 8 * * *     → Daily at 8:00 AM
0 */2 * * *   → Every 2 hours
30 9 * * 1-5  → Weekdays 9:30 AM
0 0 1 * *     → Monthly 1st at midnight
```

### Bài tập
1. Create DAG with 3 tasks (sequential)
2. Create DAG with parallel tasks
3. Use PythonOperator to read file
4. Set up schedule (daily + weekly)
5. Use Airflow UI: trigger, view logs, clear task

### Resources
| Resource | Link | Type |
|---|---|---|
| Astronomer Academy | [academy.astronomer.io](https://academy.astronomer.io/) | Free |
| Airflow Docs Tutorial | [airflow.apache.org/docs](https://airflow.apache.org/docs/apache-airflow/stable/tutorial.html) | Free |
| Data Pipelines with Airflow (book) | Bas Harenslak | Paid |

---

## Level 2: Intermediate (3 tuần)

### Task Dependencies & Branching
```python
from airflow.operators.python import BranchPythonOperator
from airflow.operators.empty import EmptyOperator

def choose_branch(**context):
    day = context["execution_date"].day_of_week
    if day < 5:
        return "weekday_task"
    return "weekend_task"

branch = BranchPythonOperator(
    task_id="branch",
    python_callable=choose_branch,
)

weekday = EmptyOperator(task_id="weekday_task")
weekend = EmptyOperator(task_id="weekend_task")
join = EmptyOperator(task_id="join", trigger_rule="none_failed_min_one_success")

branch >> [weekday, weekend] >> join
```

### Connections & Variables
```python
# Connection (stored in Airflow, not in code!)
from airflow.hooks.base import BaseHook

conn = BaseHook.get_connection("redshift_default")
host = conn.host
password = conn.password

# Variables
from airflow.models import Variable

env = Variable.get("environment", default_var="sit")
```

### TaskGroups
```python
from airflow.utils.task_group import TaskGroup

with TaskGroup("transform") as transform_group:
    task_a = BashOperator(task_id="model_a", bash_command="dbt run -s model_a")
    task_b = BashOperator(task_id="model_b", bash_command="dbt run -s model_b")
    task_a >> task_b

extract >> transform_group >> load
```

### Sensors (wait for conditions)
```python
from airflow.sensors.filesystem import FileSensor
from airflow.sensors.external_task import ExternalTaskSensor

# Wait for file
wait_file = FileSensor(
    task_id="wait_for_file",
    filepath="/data/daily_export.csv",
    poke_interval=60,
    timeout=3600,
)

# Wait for another DAG
wait_upstream = ExternalTaskSensor(
    task_id="wait_for_ingestion",
    external_dag_id="data_ingestion_dag",
    external_task_id="load_complete",
)
```

### Bài tập
1. Create DAG with branching logic
2. Use Connections to read from database
3. Implement retry + alerting (email on failure)
4. Create TaskGroup for dbt models
5. Use sensor to wait for upstream DAG

### Resources
| Resource | Link | Type |
|---|---|---|
| Astronomer Guides | [docs.astronomer.io/learn](https://docs.astronomer.io/learn) | Free |
| Airflow Best Practices | [airflow.apache.org/docs/best-practices](https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html) | Free |

---

## Level 3: Advanced (4 tuần)

### Dynamic DAGs
```python
# Generate tasks dynamically from config
import yaml

with open("dags/dag_settings.yml") as f:
    config = yaml.safe_load(f)

for dag_config in config["dags"]:
    with DAG(dag_id=dag_config["name"], schedule=dag_config["schedule"]) as dag:
        for task in dag_config["tasks"]:
            BashOperator(task_id=task["name"], bash_command=task["command"])
```

### Astronomer Cosmos (dbt + Airflow)
```python
# Cosmos integration
from cosmos import DbtTaskGroup, ProjectConfig, ProfileConfig, RenderConfig
from cosmos.constants import TestBehavior

dbt_project = DbtTaskGroup(
    group_id="dbt_transform",
    project_config=ProjectConfig(
        dbt_project_path="/opt/airflow/dags/dbt/project",
    ),
    profile_config=ProfileConfig(
        profile_name="my_project",
        target_name="dev",
    ),
    render_config=RenderConfig(
        test_behavior=TestBehavior.AFTER_EACH,
        select=["tag:daily"],
    ),
    operator_args={
        "vars": '{"business_date": "{{ ds }}", "run_id": "{{ run_id }}"}',
    },
)
```

### SLA & Alerting
```python
from airflow.exceptions import AirflowSensorTimeout

with DAG(
    dag_id="critical_pipeline",
    sla_miss_callback=sla_alert_function,
) as dag:
    
    task = BashOperator(
        task_id="critical_task",
        bash_command="dbt run -s critical_model",
        sla=timedelta(hours=2),  # Must complete within 2h
    )
```

### Custom Operators
```python
from airflow.models import BaseOperator

class DataQualityOperator(BaseOperator):
    def __init__(self, table: str, checks: list, **kwargs):
        super().__init__(**kwargs)
        self.table = table
        self.checks = checks

    def execute(self, context):
        # Run quality checks
        for check in self.checks:
            result = run_check(self.table, check)
            if not result:
                raise ValueError(f"Quality check failed: {check}")
```

### Production Patterns
```python
# Idempotent DAGs (re-runnable safely)
# Backfill support
airflow dags backfill -s 2024-01-01 -e 2024-01-31 my_dag

# Pool management (limit concurrent connections)
# Set pool "redshift_pool" = 5 connections max
task = BashOperator(
    task_id="query",
    bash_command="...",
    pool="redshift_pool",
)
```

### Bài tập
1. Implement Cosmos DbtTaskGroup with dbt project
2. Create dynamic DAG from YAML config
3. Build custom operator for data quality
4. Set up SLA monitoring + Slack alerts
5. Configure pools for Redshift connection limits

### Resources
| Resource | Link | Type |
|---|---|---|
| Astronomer Cosmos docs | [astronomer.github.io/astronomer-cosmos](https://astronomer.github.io/astronomer-cosmos/) | Free |
| Airflow Providers | [airflow.apache.org/docs/providers](https://airflow.apache.org/docs/apache-airflow-providers/) | Free |
| Astronomer Certification | [academy.astronomer.io](https://academy.astronomer.io/) | Free/Paid |

---
## Alternatives & Công cụ tương đương

| Tool | Description | vs Airflow |
|---|---|---|
| **Dagster** | Modern, asset-oriented orchestrator | Better dev UX, type-safe, newer |
| **Prefect** | Python-native, hybrid execution | Simpler API, less infra overhead |
| **Mage** | Modern data pipeline tool | Notebook-like UI, integrated |
| **Kestra** | Event-driven orchestrator | YAML-based, language agnostic |
| **AWS Step Functions** | AWS-native workflow | Serverless, AWS-only |
| **Google Cloud Composer** | Managed Airflow | Same Airflow, managed by GCP |
| **MWAA** | AWS Managed Airflow | Same Airflow, managed by AWS |

### Khi nào chọn gì?
- **Airflow** → Industry standard, most jobs, largest ecosystem
- **Dagster** → Greenfield project, want modern DX
- **Prefect** → Simpler orchestration needs, Python-heavy
- **Managed Airflow** → Want Airflow without infra burden
