---
layout: default
title: Limitations
parent: Management
nav_order: 11
---

# Limitations

---

## Cluster Limitations

| Description                        | Maximum Size       | Notes |
|:-----------------------------------|:-------------------|:------|
| Number of Hosts                    | Unlimited          | Only clusters with a limited number of hosts will have workloads that generate large amounts of network traffic among the hosts |
| Number of Nodes                    | Unlimited          | Only clusters with a limited number of hosts will have workloads that generate large amounts of network traffic among the hosts |
| Number of Databases | Unlimited    | Each database uses memory to establish a transaction buffer. More databases will demand more memory |
| Number of Concurrent Queries       | Unlimited          | Each aggregator node only executes a limited number of queries (192 by default) at a time. Performance improves with the growth of the cluster size |
| Number of Simultaneous Connections | 100,000/Aggregator | |

## Database Limitations

| Description          | Maximum Size | Notes |
|:---------------------|:-------------|:------|
| Number of Tables     | Unlimited    | Each table consumes certain amount of amount of memory overhead |
| Number of Partitions | Unlimited    | Each user can define the number of partitions |

## Table Limitations

| Description                                                      | Maximum Size | Notes |
|:-----------------------------------------------------------------|:-------------|:------|
| Number of columns in a table                                     | 4096         | |
| Row size in bytes                                                | 64KB         | The restriction does not apply to variable length strings, such as VARCHAR, VARBINARY, TEXT, etc. |
| Character name length (applicable to table name and column name) | 64           | |
| Number of indexes in a table                                     | 63           | |
| Number of columns for an index                                   | 32           | |
| Number of joined tables in a SELECT statement                    | 253          | |
