---
layout: default
nav_exclude: true
---

## Python example code

Make sure you have installed the rapids db python lib before run this

```python
# 载入 pyRDP
import pyRDP as pyRDP

# 打开数据库连接
conn = pyRDP.connect(host = "domain_name or ip_address",
                     port = 4333,
                     user = 'RAPIDS',
                     password = 'rapids',
                     catalog = 'connector_name',
                     schema = "database_name")

# 使用 cursor()方法获取操作游标
cursor = conn.cursor()
# 使用 execute 方法执行 SQL 语句
sql = "select * from table_name;" 

# 使用 fetchone() 方法获取一条查询结果数据 data = cursor.fetchone()
# 使用 fetchall() 方法获取所有查询结果数据 cursor.execute(sql) # 必须要执行
results = cursor.fetchall()

# 关闭数据库连接
conn.close()
```
