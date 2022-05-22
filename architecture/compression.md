---
layout: default
title: Compression
parent: Architecture
nav_order: 8
---

# Compression

---

When data is too large to fit in memory or too old to be stored in memory cost-effectively, the column store provides a good hybrid data storage option.  A column store treats each column as a unit and stores data in each column at the same physical location. This method has two advantages. First, it enables each column to be stored and scanned separately. As a result, the database can just scan the columns required by a query while keeping good caching locality. Second, a column store is ideal for data compression. Column data can be sorted by selected column store index keys. Duplicated column data or similar data values can be easily compressed together to minimize the amount of data that a query needs to read from disk, generating fast query performance while reducing data storage cost to process massive data. In the following figure, the Price column is sorted and used as the index of the column store table.

![RapidsDB 4.0 Data Compression Workflow](./data-compression.jpg)\
*RapidsDB 4.0 Data Compression Workflow*
