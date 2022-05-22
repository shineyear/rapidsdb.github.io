---
layout: default
title: Python Example Code
parent: Development
nav_order: 2
---

# Python Example Code

---

Make sure you have installed the RapidsDB Python library before running this.

```python
import pyRDP as pyRDP

conn = pyRDP.connect(host = "domain_name or ip_address",
                     port = 4333,
                     user = 'RAPIDS',
                     password = 'rapids',
                     catalog = 'connector_name',
                     schema = "database_name")

cursor = conn.cursor()
sql = "select * from table_name;" 

cursor.execute(sql)
results = cursor.fetchall()

conn.close()
```
