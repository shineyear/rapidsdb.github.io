---
layout: default
title: GEO Polygon SQL
parent: Development
nav_order: 3
---

# GEO Polygon SQL

---

## Create Table

```sql
create table table_name ( 
  agency varchar(10),
  area varchar(20),
  availablelots int,
  carparkid varchar(10),
  development varchar(50),
  location GEOGRAPHYPOINT,
  lottype varchar(10),
  source varchar(10), 
  lat double, 
  lon double, 
  index(location)
);
   
create table table_name_2 (
  id varchar(100) default null,
  FEATID varchar(100) default null,
  LVL varchar(100) default null,
  QUALITY varchar(100) default null,
  TYPE varchar(100) default null,
  INC_CRC varchar(100) default null,
  FMEL_UPD_D varchar(100) default null,
  GEOMETRY GEOGRAPHY default null,
  index (GEOMETRY) with (resolution = 8),
  index (FEATID)
);
```

## Use Python to Insert JSON Data or Load From CSV

```python
import pyRDP as pyRDP
import json

with open('lta_carpark.json') as f:
  data = json.load(f)

conn = pyRDP.connect(host = "domain_name or ip_address",
                     port = 4333,
                     user = 'RAPIDS',
                     password = 'rapids',
                     catalog = 'connector_name',
                     schema = "database_name")

cursor = conn.cursor()
for i in data:
    agency = i["agency"]
    area = i["area"]
    availablelots = i["availablelots"]
    carparkid = i["carparkid"]
    development = i["development"]
    lastupdated = i["lastupdated"]
    dataz = i["location"]
    lat = i["location"]["lat"]
    lon = i["location"]["lon"]
    lottype = i["lottype"]
    source = i["source"]

    sql = "INSERT INTO table_name (agency, area, availablelots, carparkid, development, location, lat, lon, lottype, source) VALUES ('"+agency+"', '"+area+"', "+str(availablelots)+", '"+carparkid+"', '"+development+"', 'POINT("+lon+" "+lat+")', "+lat+", "+lon+", '"+lottype+"', '"+source+"')"
    cursor.execute(sql)

conn.close()
```

```sql
load data local infile "infile.csv"
into table table_name_2
FIELDS TERMINATED BY '|'
ENCLOSED BY '"';
```

## Search the Record

```sql
SELECT carparkid, location FROM table_name WHERE ROUND(GEOGRAPHY_DISTANCE("POINT(1.85718 2.29375)", location), 0) < 5000;

SELECT c.carparkid, h.FEATID FROM table_name c, table_name_2 h WHERE GEOGRAPHY_CONTAINS(h.GEOMETRY, c.location);
```
