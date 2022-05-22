---
layout: default
title: Benchmarking
nav_order: 7
---

# Benchmarking

---

## TPC-H

TPC-H is a decision support benchmark. It consists of a suite of business-oriented ad-hoc queries and concurrent data modifications used to test performance of databases. Rather than the ECS performance, the TPC-H statements are used to test the SQL compatibility and execution speed of the RapidsDB real-time Cloud data warehouse.

| Scenario                                     | Number of Nodes | Total Execution Time (ms) | Average Execution Time (ms) | Pass |
|:---------------------------------------------|:----------------|:--------------------------|:----------------------------|:-----|
| Loading 500G TPC-H data from OBS             | 3               | 1,400,062                 | --                          | Yes  |
|                                              | 6               | 758,527                   | --                          | Yes  |
|                                              | 9               | 564,700                   | --                          | Yes  |
| Executing 22 TPC-H query statements in order | 3               | 294,166                   | 13,371                      | Yes  |
|                                              | 6               | 155,208                   | 7,055                       | Yes  |
|                                              | 9               | 111,705                   | 5,077                       | Yes  |
| Executing 3 TPC-H concurrent queries         | 3               | 841,490                   | 38,249                      | No   |
|                                              | 6               | 416,532                   | 18,933                      | Yes  |
|                                              | 9               | 285,308                   | 12,968                      | Yes  |
| Executing 5 TPC-H concurrent queries         | 6               | 696,046                   | 31,638                      | Yes  |
|                                              | 9               | 478,559                   | 21,753                      | Yes  |
| Executing 10 TPC-H concurrent queries        | 6               | 1,405,120                 | 63,869                      | Yes  |
|                                              | 9               | 972,564                   | 44,207                      | Yes  |
