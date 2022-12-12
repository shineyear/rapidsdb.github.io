---
layout: default
title: KAFKA RPDSQL Pipeline
parent: Development
nav_order: 1
---

# KAFKA RPDSQL Pipeline

---

```sql
create table beta (num VARCHAR(20), price VARCHAR(20));

CREATE PIPELINE `mypipeline` AS LOAD DATA KAFKA '10.1.24.240:9092/quickstart'
INTO TABLE beta
FIELDS TERMINATED BY ',' ENCLOSED BY '' ESCAPED BY '\\'
LINES TERMINATED BY '\n' STARTING BY '';

test pipeline mypipeline;

show pipelines;

start pipeline mypipeline;
```

![Branching](./kafka_pipeline.png)

