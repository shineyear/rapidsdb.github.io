---
layout: default
title: High Concurrency
parent: Architecture
nav_order: 5
---

# High Concurrency

---

Concurrency control is the key technology of a database management system to guarantee database consistency. Without concurrency control, if a user is reading from a database at the same time as another user is writing to it, the reader might encounter inconsistent data. Although a read-write lock could be applied to make all readers wait until the writer is done, it would diminish the performance advantage that the parallel processing brings and make the real-time data analytics impossible.

RapidsDB uses multi-version concurrency control (MVCC) and lock-free data structures to provide concurrent access to the database and prevent read and write operations from blocking each other in a multi-user environment. They enable RapidsDB to maintain high data accessibility and consistent data views in the case of a large number of concurrent users without the database performance loss.

Concurrency is also one of the most important indicators used to evaluate the performance of a database. RapidsDB's distributed Query Optimizer can evenly distribute and process a large number of workloads across nodes to maximize CPU efficiency.
