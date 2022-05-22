---
layout: default
title: Security Management
parent: Management
nav_order: 8
---

# Security Management
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## ALTER USER

Syntax:

```sql
ALTER USER user[@host] [IDENTIFIED BY 'password'] [SET DEFAULT RESOURCE POOL = poolname] [REQUIRE {SSL | NONE}]
```

Examples:

```sql
CREATE USER tom WITH DEFAULT RESOURCE POOL = general;
ALTER USER tom WITH DEFAULT RESOURCE POOL = executive;

ALTER USER 'test' REQUIRE SSL;
```

## CREATE GROUP

Syntax:

```sql
CREATE GROUP 'group_name'
```

## CREATE ROLE

Syntax:

```sql
CREATE ROLE 'role_name'
```

## CREATE USER

Syntax:

```sql
CREATE USER user[@host] [IDENTIFIED BY 'password'] [WITH DEFAULT RESOURCE POOL = poolname]
```

Examples:

```sql
CREATE USER tom;

GRANT SELECT ON test.* TO 'tom'@'%';
```

## CURRENT_USER

Syntax:

```sql
CURRENT_USER()

USER()
```

Examples:

```sql
SELECT CURRENT_USER();

SELECT USER();
```

## DROP GROUP

Syntax:

```sql
DROP GROUP [IF EXISTS] 'group_name'
```

## DROP ROLE

Syntax:

```sql
DROP ROLE [IF EXISTS] 'role_name'
```

## DROP USER

Syntax:

```sql
DROP USER [IF EXISTS] user [, user] ...
```

## GRANT

Syntax:

```sql
GRANT priv_type [, priv_type [ ... ]] ON priv_level
TO user_or_role [, user_or_role [ ... ]]
[WITH GRANT OPTION]
[REQUIRE {SSL | NONE}]
```

where

```text
priv_type:
ALL PRIVILEGES
| SELECT
| INSERT
| UPDATE
| DELETE
| CREATE
| DROP
| RELOAD
| PROCESS
| FILE READ
| FILE WRITE
| INDEX
| ALTER
| SHOW METADATA
| SUPER
| CREATE TEMPORARY TABLES
| LOCK TABLES
| CREATE VIEW
| SHOW VIEW
| CREATE USER
| CLUSTER
| ALTER VIEW
| DROP VIEW
| BACKUP
| CREATE DATABASE
| DROP DATABASE
| CREATE PIPELINE
| DROP PIPELINE
| ALTER PIPELINE
| START PIPELINE
| SHOW PIPELINE
| EXECUTE
| CREATE ROUTINE
| ALTER ROUTINE

priv_level:
*
| *.*
| database.*
| database.table

user_or_role:
user [, user]
| role

user:
'user_name'@'host_name' [IDENTIFIED BY 'password']

role:
ROLE 'role_name'
```

Examples:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

## GRANT ROLE

Syntax:

```sql
GRANT ROLE 'role_name' TO 'group_name'
```

Examples:

```sql
GRANT ROLE 'full_backup_role' TO 'backup_admins_group';
```

## REVOKE

Syntax:

```sql
REVOKE priv_type [, priv_type] ... ON priv_level
FROM user [, user] ...
```

Examples:

```sql
REVOKE INSERT ON *.* FROM 'user'@'%';

REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'user'@'%';
```

## REVOKE GROUP

Syntax:

```sql
REVOKE GROUP 'role_name' FROM user
```

Examples:

```sql
REVOKE GROUP 'backup_admins' FROM 'username'@'%';
```

## REVOKE ROLE

Syntax:

```sql
REVOKE ROLE 'role_name' FROM 'group_name'
```

Examples:

```sql
REVOKE ROLE ‘full_backups_role' FROM 'backup_admins';
```

## SET PASSWORD

Syntax:

```sql
SET PASSWORD FOR 'username'@'host' = PASSWORD('password')
```

## SHOW GRANTS

Syntax:

```sql
SHOW GRANTS

SHOW GRANTS FOR USER 'user'@'host_name'

SHOW GRANTS FOR ROLE 'role_name'
```

## SHOW GROUPS

Syntax:

```sql
SHOW GROUPS

SHOW GROUPS FOR USER 'user'@'host_name'

SHOW GROUPS FOR ROLE 'role_name'
```

## SHOW ROLES

Syntax:

```sql
SHOW ROLES

SHOW ROLES FOR USER 'user'@'host_name'

SHOW ROLES FOR GROUP 'group_name'
```

## SHOW USERS

Syntax:

```sql
SHOW USERS

SHOW USERS FOR ROLE 'role_name'

SHOW USERS FOR GROUP 'group_name'
```

## Permissions Matrix

| SQL | Allowed Targets in Default Mode | Allowed Targets in Strict Mode | Notes |
|:----|:--------------------------------|:-------------------------------|:------|
| USAGE | Cluster | Cluster | Connect, show variables |
| SELECT | Cluster, Database, Table | Database, Table | Select rows |
| INSERT | Cluster, Database, Table | Database, Table | Insert rows |
| UPDATE | Cluster, Database, Table | Database, Table | Update cells of existing rows |
| DELETE | Cluster, Database, Table | Database, Table | Delete rows |
| CREATE | Cluster, Database, Table | Database, Table | Create tables |
| DROP | Cluster, Database, Table | Database, Table | Drop tables |
| RELOAD | Cluster, Database | Cluster. Database | Load backups into database |
| FILE WRITE | Cluster | Cluster | File write access |
| PROCESS | Cluster | Cluster | View and kill queries |
| FILE READ | Cluster | Cluster | File read access |
| WITH GRANT OPTION | | (disallowed) | WITH GRANT OPTION  disabled in strict mode |
| INDEX | Cluster, Database, Table | Database, Table | Create and drop indexes |
| ALTER | Cluster, Database, Table | Database, Table | Alter tables (including indexes) |
| SHOW METADATA | Cluster | Cluster | Show all metadata |
| SUPER | Cluster | Cluster | Set global variables, modify resource pool settings |
| CREATE TEMPORARY TABLES | Cluster, Database | Database | Create temporary tables |
| LOCK TABLES | Cluster, Database | Cluster, Database | Lock tables (read and write) |
| REPLICATION | Cluster, Database | Cluster, Database | Read data for replication |
| CREATE VIEW | Cluster, Database | Database | Create views |
| ALTER VIEW | Cluster, Database, View | Database, View | Alter views |
| DROP VIEW | Cluster, Database, Table | Database, Table | Drop views |
| SHOW VIEW | Cluster, Database, View | Database, View | Show VIEW definitions |
| CLUSTER | Cluster | Cluster | Cluster administration, including replication, partition movement, and topology. Includes all permissions in SHOW METADATA. |
| BACKUP | Cluster, Database | Cluster. Database | Take backups and snapshots |
| CREATE USER | Cluster | Cluster | Create and drop users (no grants) |
| GRANT | Cluster, Database | Cluster. Database | Grant / revoke permissions, manage roles & groups. Distinct from  WITH GRANT OPTION. |
| DROP DATABASE | Cluster, Database | Cluster. Database | Drop databases |
| CREATE DATABASE | Cluster, Database | Cluster. Database | Create databases |
| CREATE ROUTINE | Cluster, Database, Function | Database, Function | Create extensibility functions or procedures |
| ALTER ROUTINE | Cluster, Database, Function | Database, Function | Replace or delete extensibility functions or procedures |
| EXECUTE | Cluster, Database, Function | Database, Function | Execute extensibility functions or procedures |
| CREATE PIPELINE | Cluster, Database, Table | Database, Table | Create pipelines |
| DROP PIPELINE | Cluster, Database, Table | Database, Table | Drop pipelines |
| START PIPELINE | Cluster, Database, Table | Database, Table | Start pipelines |
| ALTER PIPELINE | Cluster, Database, Table | Database, Table | Alter pipelines |
| SHOW PIPELINE | Cluster, Database, Table | Database, Table | Show pipelines |
