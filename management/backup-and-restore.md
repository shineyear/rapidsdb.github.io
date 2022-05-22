---
layout: default
title: Backup and Restore
parent: Management
nav_order: 9
---

# Backup and Restore

---

## For RPDSQL

Syntax:

```sql
BACKUP [DATABASE] db_name [WITH { INIT | DIFFERENTIAL | SPLIT PARTITIONS [BY 2] }] TO "backup_path" [(OPTION resource_pool = resource_pool_name)]

RESTORE [DATABASE] db_name FROM "backup_path" [WITH FILE incr_backup_id] [sync_options] [(OPTION resource_pool = resource_pool_name)]
```

Example:

```sql
BACKUP testdata TO "/home/testdata";

RESTORE DATABASE testdata from "/home/testdata";
```

## For MOXE

Refer to [RapidsDB Installation and Management Guide](../downloads/RapidsDB_Installation_and_Management_Guide_Release_v4.3.3.pdf):

* Chapter 8.5 Backing up and Restoring MOXE Tables
