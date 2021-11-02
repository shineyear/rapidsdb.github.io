---
layout: default
---

[back](./)

## GEO Polygon SQL

1. Create table
```sql
create table table name ( 
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
```
2. Use python insert json data
```python
# 载入 pyRDP
import pyRDP as pyRDP
import json

# 打开 json 文件
with open('lta_carpark.json') as f:
  data = json.load(f)


# 打开数据库连接
conn = pyRDP.connect(host = "domain name or ip address",
                     port = 4333,
                     user = 'RAPIDS',
                     password = 'rapids',
                     catalog = 'connector name',
                     schema = "database name")

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
    sql = "INSERT INTO table name (agency, area, availablelots, carparkid, development, location, lat, lon, lottype, source) VALUES ('"+agency+"', '"+area+"', "+str(availablelots)+", '"+carparkid+"', '"+development+"', 'POINT("+lon+" "+lat+")', "+lat+", "+lon+", '"+lottype+"', '"+source+"')"
    cursor.execute(sql)
    


# 关闭数据库连接
conn.close()

```
3. Search the record
```sql
SELECT carparkid, location FROM table name WHERE ROUND(GEOGRAPHY_DISTANCE("POINT(1.85718 2.29375)", location), 0) < 5000;
```


