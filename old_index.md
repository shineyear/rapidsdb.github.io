---
layout: default
title: What is RapidsDB
nav_order: 99
---

## What is RapidsDB?

RapidsDB is a fully parallel, distributed, in-memory federated query system that is designed to support
complex analytical SQL queries running against a set of different data stores.

RapidsDB provides unified SQL access to a wide variety of data sources, which can include relational and
non-relational data sources. Data can be joined across all of the data sources.

![Branching](/images/architecture.jpg)

## What is Rapids Shell?

The command line toolkit for remote access rapids db, full support for windows/linux/mac.
For the UI tool we suggest [DBeaver](https://dbeaver.io) (need to use our customized JDBC driver)

## What is RPDSQL?

RPDSQL is the storage engine for rapids db, fully support ANSI SQL standard, High availability
and parallel compute

## What is the current release version?

| software      | version    | date      |
|:--------------|:-----------|:----------|
| RapidsDB      | 4.2.3.3    | 20211101  |
| RPDSQL        | 2.0.6.     | 20211101  |
| Rapids Shell  | 4.0.6      | 20211101  |
| JDBC          | 4.0.6      | 20211101  |
| Python Lib    | 4.0.0      | 20211101  |

## Data source support?

* Any JDBC database
* Hadoop HDFS
* Hive
* S3
* Rabbit mq
* Json or csv file from Elasticsearch, Redis, Google Bigquery

## How to get a license?

Please email vincent@rapidsdb.sg or yylai@rapidsdb.sg for a trial license.

## API and Example Code

[How to install Rapidsdb](./rapidsdb_install.html)

[How to install RPDSQL](./rpdsql_install.html)

[AWS S3 connector](./s3_connector.html)

[Hadoop HDFS connector](./hdfs.html)

[Elasticsearch json import/export](./elasticsearch_json.html)

[Redis csv file import](./redis_csv.html)

[GEO Polygon SQL](./geo_sql.html)

[Python Code Example](./python_example.html)

[TPCH performance benchmark](./tpch.html)

[ALL IN ONE RapidsDB buildup](./all_in_one.html)

[Rapids db container migration](./container_migration.html)

[AWS EC2 import qcow2 image as snapshot](./snapshot.html)

[High Availability design](./ha.html)

## Documents Download

* [Install guide (pdf)](/download/RapidsDB_Installation_and_Management_Guide_Release_v4.2.3.2%20(1).pdf)
* [JDBC driver (zip)](/download/rapids-jdbc-4.0.6.jar.zip)
* [Python lib (whl)](/download/pyRDP-4.0.0-py3-none-any.whl)
* [Rapids Shell (zip)](https://drive.google.com/file/d/1HzmZSIP6C3dEW4EiFI1XQY1lwawiEzEM/view?usp=sharing)
* [Optimization guide (pdf)](/download/数据库性能优化手册.pdf)
* [Benchmark compare (xslt)](/download/TPCH结果对比%20(1).xlsx)
* [Testing guide (pdf)](/download/testing-guide-EN.pdf)
* [Rapids DB SQL manuals (doc)](/download/RapidsDB%20SQL%E8%AF%AD%E6%B3%95%E6%8C%87%E5%8D%97v2.0.doc)
* [RapidsDB Technical White Paper (pdf)](/download/RapidsDB%20Technical%20White%20Paper.pdf)
* [RapidsDB DEMO (pdf)](/download/RapidsDB%20DEMO%20V1.2.pdf)