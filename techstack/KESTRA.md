# ⚡ Kestra — Learning Guide (Beginner → Advanced)

## Tổng quan

**Kestra** là một event-driven orchestrator sử dụng declarative YAML workflows. Khác với Dagster/Airflow (Python-centric), Kestra cho phép orchestrate bất kỳ ngôn ngữ nào (Python, SQL, Shell, R, Node.js) qua YAML config — không cần viết code để định nghĩa pipeline.

**Use cases:**
- Event-driven data pipelines
- Multi-language orchestration (Python + SQL + Shell)
- Microservices coordination
- Scheduled ETL/ELT jobs

**Ai dùng:** Data Engineers, DevOps, Platform Engineers, teams đa ngôn ngữ

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Flow** | Một workflow definition (YAML file) |
| **Task** | Một step trong flow (run script, query DB, call API) |
| **Trigger** | Cách khởi chạy flow (schedule, webhook, event) |
| **Namespace** | Logical grouping cho flows (like folders) |
| **Inputs** | Parameters truyền vào flow khi execute |
| **Outputs** | Data produced bởi tasks, share giữa tasks |

### Setup

```bash
# Docker (recommended for local dev)
docker run --pull=always --rm -it -p 8080:8080 \
  --user=root \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /tmp:/tmp \
  kestra/kestra:latest server local

# Access UI at http://localhost:8080
```

```bash
# Docker Compose
curl -o docker-compose.yml \
  https://raw.githubusercontent.com/kestra-io/kestra/develop/docker-compose.yml
docker compose up -d
```

### First Flow

```yaml
# flows/hello_world.yml
id: hello_world
namespace: tutorial
description: My first Kestra flow

inputs:
  - id: greeting
    type: STRING
    defaults: "Hello, World!"

tasks:
  - id: say_hello
    type: io.kestra.plugin.core.log.Log
    message: "{{ inputs.greeting }}"

  - id: current_date
    type: io.kestra.plugin.scripts.shell.Commands
    commands:
      - echo "Current date is $(date)"

  - id: python_task
    type: io.kestra.plugin.scripts.python.Script
    script: |
      import json
      data = {"status": "success", "items": 42}
      print(json.dumps(data))
```

### Schedule Trigger

```yaml
id: scheduled_etl
namespace: production

triggers:
  - id: daily_schedule
    type: io.kestra.plugin.core.trigger.Schedule
    cron: "0 6 * * *"  # Every day at 6 AM

tasks:
  - id: extract
    type: io.kestra.plugin.scripts.python.Script
    script: |
      import requests
      response = requests.get("https://api.example.com/data")
      print(f"Extracted {len(response.json())} records")

  - id: notify
    type: io.kestra.plugin.core.log.Log
    message: "ETL completed at {{ now() }}"
```

### Bài tập

1. **Install & First Flow** — Chạy Kestra Docker, tạo flow đầu tiên qua UI
2. **Input parameters** — Flow nhận input (date, table_name), dùng trong tasks
3. **Multi-task flow** — Flow 3 tasks: shell → python → log output
4. **Schedule** — Thêm cron schedule, xem execution history trong UI

### Resources

- 📖 [Kestra Docs](https://kestra.io/docs)
- 📖 [Getting Started Tutorial](https://kestra.io/docs/getting-started)
- 💻 [Kestra GitHub](https://github.com/kestra-io/kestra)
- 🎬 [Kestra YouTube](https://www.youtube.com/@kaborestra)


---

## Level 2: Intermediate (2–4 tuần)

### Flow Triggers (Event-driven)

```yaml
id: event_driven_pipeline
namespace: production

triggers:
  # Trigger khi flow khác hoàn thành
  - id: after_extraction
    type: io.kestra.plugin.core.trigger.Flow
    conditions:
      - type: io.kestra.plugin.core.condition.ExecutionFlowCondition
        namespace: production
        flowId: extract_raw_data
      - type: io.kestra.plugin.core.condition.ExecutionStatusCondition
        in:
          - SUCCESS

tasks:
  - id: transform
    type: io.kestra.plugin.scripts.python.Script
    script: |
      print("Triggered after extraction completed!")
```

### Conditional Logic

```yaml
id: conditional_flow
namespace: production

inputs:
  - id: environment
    type: STRING
    defaults: "dev"

tasks:
  - id: check_env
    type: io.kestra.plugin.core.flow.If
    condition: "{{ inputs.environment == 'production' }}"
    then:
      - id: prod_task
        type: io.kestra.plugin.scripts.shell.Commands
        commands:
          - echo "Running in PRODUCTION mode"
    else:
      - id: dev_task
        type: io.kestra.plugin.scripts.shell.Commands
        commands:
          - echo "Running in DEV mode - using sample data"
```

### Subflows

```yaml
# Parent flow
id: main_pipeline
namespace: production

tasks:
  - id: run_extraction
    type: io.kestra.plugin.core.flow.Subflow
    flowId: extract_data
    namespace: production
    inputs:
      source_table: customers
      target_date: "{{ now() | dateAdd(-1, 'DAYS') | date('yyyy-MM-dd') }}"
    wait: true

  - id: run_transformation
    type: io.kestra.plugin.core.flow.Subflow
    flowId: transform_data
    namespace: production
    inputs:
      source_table: customers
    wait: true
```

### Secrets Management

```yaml
id: secure_pipeline
namespace: production

tasks:
  - id: connect_db
    type: io.kestra.plugin.jdbc.postgresql.Query
    url: jdbc:postgresql://{{ secret('DB_HOST') }}:5432/analytics
    username: "{{ secret('DB_USER') }}"
    password: "{{ secret('DB_PASSWORD') }}"
    sql: |
      SELECT count(*) as total FROM customers
      WHERE created_at >= CURRENT_DATE - INTERVAL '1 day'
```

### Bài tập

1. **Flow trigger** — Tạo 2 flows: flow B trigger khi flow A thành công
2. **Conditional** — Flow xử lý khác nhau dựa trên input (dev vs prod)
3. **Subflows** — Chia pipeline lớn thành subflows reusable
4. **Error handling** — Thêm retry logic và error notifications

### Resources

- 📖 [Flow Triggers](https://kestra.io/docs/workflow-components/triggers)
- 📖 [Conditions & Branching](https://kestra.io/docs/workflow-components/conditions)
- 📖 [Subflows](https://kestra.io/docs/workflow-components/subflows)
- 📖 [Secrets](https://kestra.io/docs/concepts/secret)

---

## Level 3: Advanced (4–8 tuần)

### Custom Plugins (Java)

```java
// src/main/java/com/example/MyCustomTask.java
package com.example;

import io.kestra.core.models.annotations.Plugin;
import io.kestra.core.models.tasks.RunnableTask;
import io.kestra.core.runners.RunContext;
import io.kestra.core.models.tasks.Task;

@Plugin
public class MyCustomTask extends Task implements RunnableTask<MyCustomTask.Output> {
    
    private String inputParam;
    
    @Override
    public Output run(RunContext runContext) throws Exception {
        String rendered = runContext.render(inputParam);
        // Custom logic here
        return Output.builder()
            .result("Processed: " + rendered)
            .build();
    }
    
    @lombok.Builder
    @lombok.Getter
    public static class Output implements io.kestra.core.models.tasks.Output {
        private final String result;
    }
}
```

### Event-Driven Architecture with Kafka

```yaml
id: kafka_event_processor
namespace: production

triggers:
  - id: kafka_trigger
    type: io.kestra.plugin.kafka.Trigger
    properties:
      bootstrap.servers: kafka:9092
    topic: user_events
    groupId: kestra-consumer
    serdeType: JSON
    maxRecords: 100
    interval: PT30S

tasks:
  - id: process_events
    type: io.kestra.plugin.scripts.python.Script
    script: |
      import json
      events = json.loads('{{ trigger.value }}')
      print(f"Processing {len(events)} events")
      
      # Business logic
      for event in events:
          if event.get("type") == "purchase":
              print(f"Purchase: {event['amount']}")

  - id: write_to_warehouse
    type: io.kestra.plugin.jdbc.postgresql.Query
    url: jdbc:postgresql://warehouse:5432/analytics
    username: "{{ secret('WH_USER') }}"
    password: "{{ secret('WH_PASSWORD') }}"
    sql: |
      INSERT INTO processed_events (event_data, processed_at)
      VALUES ('{{ outputs.process_events.vars.result }}', NOW())
```

### Enterprise Features

```yaml
# Multi-tenancy with namespaces
id: tenant_pipeline
namespace: org.team_a.production

# Namespace-level variables
variables:
  warehouse_host: "{{ secret('TEAM_A_WH_HOST') }}"
  alert_channel: "#team-a-alerts"

# Worker groups for resource isolation
tasks:
  - id: heavy_computation
    type: io.kestra.plugin.scripts.python.Script
    workerGroup:
      key: high-memory
    script: |
      import pandas as pd
      # Process large dataset
      df = pd.read_parquet("s3://bucket/large_file.parquet")
      print(f"Processed {len(df)} rows")
```

### Bài tập

1. **Kafka integration** — Flow consume Kafka events, process, write to DB
2. **Custom plugin** — Build Java plugin đơn giản, deploy vào Kestra
3. **Multi-tenant** — Setup namespaces cho multiple teams với isolation
4. **High availability** — Deploy Kestra cluster (multiple workers + executor)

### Resources

- 📖 [Plugin Development](https://kestra.io/docs/developer-guide/plugins)
- 📖 [Kafka Plugin](https://kestra.io/plugins/plugin-kafka)
- 📖 [Enterprise Edition](https://kestra.io/enterprise)
- 📖 [Deployment Guide](https://kestra.io/docs/installation)
- 💻 [Kestra Plugin Template](https://github.com/kestra-io/plugin-template)

---

## Alternatives & Công cụ tương đương

| Tool | Approach | Strengths | Weaknesses |
|------|----------|-----------|------------|
| **Kestra** | YAML declarative | Language-agnostic, event-driven, UI | Java-based, newer |
| **Airflow** | Python DAGs | Mature, huge ecosystem | Python-only definitions |
| **Dagster** | Asset-oriented | Testing, lineage, dbt-native | Python-centric |
| **Prefect** | Python flows | Easy, dynamic, cloud-native | Python-only |
| **Mage** | Notebook blocks | Visual, interactive | Smaller community |

### Khi nào chọn gì?

- **Kestra** → Multi-language team, event-driven patterns, YAML preference, cần UI đẹp
- **Airflow** → Python team, mature ecosystem, already standardized
- **Dagster** → Asset-centric thinking, strong dbt integration, testing focus
- **Prefect** → Pure Python, dynamic workflows, serverless-like execution
- **Mage** → Notebook-style development, quick prototyping, small team
