---
layout: default
title: Redis CSV File Import
parent: Development
nav_order: 4
---

# Redis CSV File Import

---

## Build Redis Data

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

## Create RapidsDB Table

```sql
create table csv (
  id VARCHAR(100) NOT NULL,
  name VARCHAR(100) NOT NULL,
  category VARCHAR(100) NOT NULL,
  price VARCHAR(100) NOT NULL,
  PRIMARY KEY ( name )
);
```

## Load into RapidsDB

```sql
LOAD DATA LOCAL  INFILE 'products.csv' INTO TABLE csv COLUMNS TERMINATED BY ',' ENCLOSED BY '"';
```
