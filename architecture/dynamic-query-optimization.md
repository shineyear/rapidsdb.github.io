---
layout: default
title: Dynamic Query Optimization
parent: Architecture
nav_order: 9
---

# Dynamic Query Optimization

---

RapidsDB's dynamic query optimization provides a lightweight but powerful dynamic processing framework, which is adaptable to a very wide and extensible range of data sources. It allows the RapidsDB Federation Connectors to fully participate in the optimization process and enables a federated query to use different databases and schemas from entirely different systems in the same SQL statement.

Since a single query may involve multiple data sources, the RapidsDB Optimizer works interactively with the Connectors to determine which operations from the plan should be pushed down to which data source. Operations for a given Connector are condensed into a single building block, for which the Connector and data source will be responsible. The remaining parts of the query plan will be performed by the RapidsDB execution engine.

The knowledge-based model empowers Connectors to guide the dynamic optimization process based on the different capabilities of the underlying data sources. It helps Connectors gather heterogeneous statistics by dynamically inserting instrumentation into a condensed query, which then can be pushed down to the related data source.

RapidsDB allows the user to view a collection of disparate data sources as a single database, performing standard SQL queries against this federated view. The automated query optimization process works to find the best way to process a given query, parceling out work to the different data sources and efficiently integrating the results. This approach abstracts away the complexity of
the data preparation pipelines so that users can focus more on analyzing data to solve business problems instead of spending tremendous amounts of time simply preparing data or rearranging queries manually to adjust to different systems and maximize performance. It makes the integration of heterogenous data more efficient and agile.

For details, refer to the [Technical Whitepaper of Dynamic Query Optimization](./Dynamic_Query_Optimization_Whitepaper2022.pdf).
