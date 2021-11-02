---
layout: default
---

## AWS S3 rpdsql pipeline

1.Architecture

![Branching](https://github.com/shineyear/rapidsdb.github.io/raw/gh-pages/images/rapids%20db%20pipeline.jpg)

2.Setup aws s3 bucket, create bucket setup bucket policy, upload a test file

![Branching](https://github.com/shineyear/rapidsdb.github.io/raw/gh-pages/images/Screen%20Shot%202021-09-20%20at%201.44.59%20PM.png)

![Branching](https://github.com/shineyear/rapidsdb.github.io/raw/gh-pages/images/Screen%20Shot%202021-09-20%20at%202.21.02%20PM.png)

3.Create pipeline, You must login to rpdsql database first (not rapids db) , use rpdsql-ops/rapids command (not rapids shell)

```sql
CREATE PIPELINE p AS LOAD DATA 
S3 'rapids-db-test' 
CONFIG '{"region": "ap-southeast-1"}' 
CREDENTIALS '{"aws_access_key_id": "key_id", "aws_secret_access_key": "secret_key"}' 
SKIP DUPLICATE KEY ERRORS
INTO TABLE beta
FIELDS TERMINATED BY ',' ENCLOSED BY '' ESCAPED BY '\\' 
LINES TERMINATED BY '\n' STARTING BY '';
```

4.Test and run pipeline

![Branching](https://github.com/shineyear/rapidsdb.github.io/raw/gh-pages/images/Screen%20Shot%202021-09-21%20at%209.59.53%20AM.png)

After create and run pipeline in rpdsql database, the aws s3 data should be transfer into rpdsql database , 
now you can use rapids db connector to load data from rpdsql database and do more big data analysis job

