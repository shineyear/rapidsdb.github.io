---
layout: default
title: Index
parent: Architecture
nav_order: 6
---

# Index

---

As a standard SQL database, RapidsDB allows indexes to be created on tables to accelerate data access. But unlike most databases, such as MySQL, lock-free skip lists are the default data structure of RapidsDB instead of B-trees. Compared to B-trees for disk-based databases, skip lists are optimal for running in memory, providing extremely fast insertion while still being able to produce expected O(log(n)) lookup performance and arrange sequential traversal.

Column store indexing leverages the columnar storage technology to efficiently store and retrieve a large number of values from disk (flash memory or solid-state disk is recommended). Other types of indexes are required by RapidsDB to store all data in memory. Because column store indexes can achieve higher compression rates and reduce I/O from the disk, it is very useful for analytical workloads.

A hash index is typically created on a column that contains unique values, such as a primary key. Although hash indexes are very useful for a query to access and retrieve data by the key value quickly and accurately, they should be used for equality only. For example, a query with an equality filter for each indexed column can use hash indexes to enable a user to obtain measurable benefits for specific datasets or workloads.
