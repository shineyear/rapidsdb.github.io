## GEO Polygon SQL

1.Create table

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

2.Use python insert json data or load from csv

```python
# 载入 pyRDP
import pyRDP as pyRDP
import json

# 打开 json 文件
with open('lta_carpark.json') as f:
  data = json.load(f)


# 打开数据库连接
conn = pyRDP.connect(host = "domain_name or ip_address",
                     port = 4333,
                     user = 'RAPIDS',
                     password = 'rapids',
                     catalog = 'connector_name',
                     schema = "database_name")

# 使用 cursor()方法获取操作游标
cursor = conn.cursor()
# 获取 json 数据 
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

    # 使用 execute 方法执行 SQL 语句 插入 json 数据
    sql = "INSERT INTO table_name (agency, area, availablelots, carparkid, development, location, lat, lon, lottype, source) VALUES ('"+agency+"', '"+area+"', "+str(availablelots)+", '"+carparkid+"', '"+development+"', 'POINT("+lon+" "+lat+")', "+lat+", "+lon+", '"+lottype+"', '"+source+"')"
    cursor.execute(sql)
    


# 关闭数据库连接
conn.close()

```

```sql
load data local infile "infile.csv"
into table table_name_2
FIELDS TERMINATED BY '|'
ENCLOSED BY '"';
```

3.Search the record

```sql
SELECT carparkid, location FROM table_name WHERE ROUND(GEOGRAPHY_DISTANCE("POINT(1.85718 2.29375)", location), 0) < 5000;

SELECT c.carparkid, h.FEATID FROM table_name c, table_name_2 h WHERE GEOGRAPHY_CONTAINS(h.GEOMETRY, c.location);
```
