---
layout: default
title: Federation
parent: Architecture
nav_order: 3
---

# Federation

---

The RapidsDB Federated Connector System is composed of a set of dynamic and pluggable connectors that can access the underlying data sources of the federated database. The Connectors manage the metadata of the objects (usually tables or files) that reside in remote data stores and send the metadata as an ANSI SQL-based schema to the RapidsDB Query Execution Engine. As the data is presented as a single federated database, a user can easily identify and combine the data using ANSI-standard SQL across any of the data sources. Through the RapidsDB Federation, RapidsDB can break data silos and query heterogeneous data easily.

RapidsDB Federated Connectors are a set of dynamic and pluggable connectors used to control the access to the underlying data stores that make up the federated database. The connectors manage the metadata of objects (usually tables or files) in remote data stores and provide the metadata as ANSI-based SQL schemas to the RapidsDB Query Execution Engine, enabling users to view the objects across multiple data sources as a single federated SQL database.  

Connectors are responsible for the native data storage management and the data type conversion. The system will uniformly process data types in all data stores. Connectors provides a unified query interface independent of data sources. They enable ANSI-standard SQL queries and present data to the RapidsDB Execution Engine in rows and columns.

In order to optimize performance, it is ideal to have each underlying data source perform as much of the query as possible to reduce the amount of data that has to be pulled and processed by the RapidsDB Execution Engine. For example, when the data source is a relational database, it is typically capable of executing a join on the tables in that data source, or it can filter the database based on predicates in the query.

RapidsDB supports an optimization feature called “Adaptive Query Pushdown” to deal with this issue. With Adaptive Query Pushdown, each Connector involved in the execution of a federated query plan analyzes the plan and decides which operations can be pushed down to the data source and, for the remaining parts of the query, the optimum way to retrieve the data and transfer it to the RapidsDB Execution Engine to complete the query.  

The intelligent, connector-based Federation solution empowers enterprises to break data silos and obtain insights from multiple data sources without the expensive and time-consuming Extract-Transform-Load (ETL) process. It provides a holistic view of business and boosts real-time decision-making.
