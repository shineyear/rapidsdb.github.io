---
layout: default
---

[back](./)

# Hadoop HDFS connector

1.Create dir in hdfs

```shell
hdfs dfs -mkdir -p /test5
```

2.Create connector in RapidsDB

```sql
CREATE CONNECTOR HDFS5 TYPE HADOOP WITH hdfs='hdfs://159.138.83.27:9000', format='delimited', delimiter=',', user='hadoop', partitions_per_node 
='1' CATALOG * SCHEMA *  TABLE TEST5 (C1 INTEGER, C2 VARCHAR) WITH PATH='/test5';
```

3.Insert data into HDFS connector

```sql
insert into HDFS5.public.test5 values(5, 'abc');

select * from HDFS5.public.test5;
```
