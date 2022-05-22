---
layout: default
title: Data Distribution
parent: Architecture
nav_order: 4
---

# Data Distribution

---

RapidsDB is a highly scalable distributed system. It adopts the common concept and practice of partitioning to help separate rows or columns of a large database table into many smaller tables across multiple partitions and nodes, making the database more manageable. When a database is created, RapidsDB will automatically calculate and set a default partition number based on cluster size and machine configurations. The number can also be manually defined by a user in the CREATE DATABASE statement.

When data is loaded, RapidsDB will distribute the data across all the partitions according to the partition key of the table structure defined by the user.   Data with the same partition key will be distributed into the same partition.

When data is processed in RapidsDB's in-memory row store, partitioning enables each machine in a RapidsDB Cluster to scan fewer rows when responding to a query. For instance, the technique is very useful to improve the response time of distributed aggregate queries and filter queries with equality predicates. Moreover, by leveraging the massively parallel processing (MPP) architecture of RapidsDB, partitioning takes advantage of all compute resources across a cluster, further improving the database performance. Nodes can be added to a RapidsDB Cluster and data can be re-sharded as needed in order to expand the workloads to maintain the high performance of the database while massive datasets are processed.
