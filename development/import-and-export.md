---
layout: default
title: Import and Export
parent: Development
nav_order: 11
---

# Import and Export

---

## For RPDSQL

### Using RapidsDB

Import:

```sql
load data infile '/home/tpch100g/nation.csv' into table nation fields terminated by '|' lines terminated by '|\n';
```

Export:

Method 1

```sql
SELECT * FROM testdata INTO OUTFILE '/data/testdata.csv' FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' LINES TERMINATED BY '\n';
```

Method 2

```shell
echo "select * from test;" | ./rapids-shell.sh -s >> test.csv
```

### Using JDBC

Import:

```java
String connectorSql = "use connector tpcht";
connection.createStatement().execute(connectorSql);
String loadSql = "load data infile '/data/tpch1000g/region.csv' into table region fields terminated by '|' lines terminated by '|\n';";
connection.createStatement().execute(loadSql);
```

### Using Python

Import:

```python
import rapids

rapids.init(ip="192.168.xxx.xxx", port=8333)
connection_url = "jdbc:mysql://192.168.xxx.xxx:3307/test"
username = "root"
password = ""

table = "testdata"
table_data = rapids.import_sql_table(connection_url, table, username, password)

select_query = "SELECT * from testdata"
query_data = rapids.import_sql_select(connection_url, select_query, username, password)
```

Export:

```python
import pandas as pd
from sqlalchemy import create_engine

conn = create_engine('jdbc:rdp://192.168.xxx.xxx:3307/test')
df = pd.DataFrame({'name' : ['User 1', 'User 2', 'User 3']})
df.to_sql(name='testdata', con=conn, if_exists='replace', index=False, dtype=dtypedict)
conn.dispose()
```

### Redis CSV File Import

Build Redis data:

```shell
redis-cli HMSET product:1001 name "Anchor" category "MilkPowder" price 400

redis-cli HMSET product:1002 name "Steve" category "Milk" price 300

redis-cli HMSET product:1003 name "Johan" category "Beef" price 200

redis-cli --scan --pattern '*product*' | grep -e "^product:[^:]*$" | awk '{split($0,a,":"); printf("\"%s\",", a[2]);  system("redis-cli --csv hmget " $0 " name category price")}' > product.csv
```

product.csv:

```csv
"1003","Johan","Beef","200"
"1002","Steve","Milk","300"
"1001","Anchor","MilkPowder","400"
```

Create RapidsDB table:

```sql
create table csv (
  id VARCHAR(100) NOT NULL,
  name VARCHAR(100) NOT NULL,
  category VARCHAR(100) NOT NULL,
  price VARCHAR(100) NOT NULL,
  PRIMARY KEY ( name )
);
```

Load into RapidsDB:

```sql
LOAD DATA LOCAL INFILE 'products.csv' INTO TABLE csv COLUMNS TERMINATED BY ',' ENCLOSED BY '"';
```

## For MOXE

Refer to [RapidsDB Installation and Management Guide](../downloads/RapidsDB_Installation_and_Management_Guide_Release_v4.3.3.pdf):

* Chapter 11 IMPORT/EXPORT Using IMPEX Connector
