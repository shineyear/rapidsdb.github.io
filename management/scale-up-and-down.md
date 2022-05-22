---
layout: default
title: Scale Up and Down
parent: Management
nav_order: 10
---

# Scale Up and Down

---

There are two ways to scale up:

- Increase storage or nodes

  ```shell
  ./rapids-manager rpdsql-install
  ```

- Increase the number of machines

  ```shell
  ./rapids-manager agent-deploy
  ./rapids-manager rpdsql-install
  ```

Finally, log in to the database and execute

```sql
rebalance partitions on dbname;
```
