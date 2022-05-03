---
layout: default
title: Security
parent: Development
nav_order: 17
---

# Security
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## SSL

## Authentication

## User Privilege

### ALTER USER

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

### CREATE GROUP

Syntax:

```sql
CREATE GROUP 'group_name'
```

### CREATE ROLE

Syntax:

```sql
CREATE ROLE 'role_name'
```

### CREATE USER

Syntax:

```sql
CREATE USER user[@host] [IDENTIFIED BY 'password'] [WITH DEFAULT RESOURCE POOL = poolname]
```

Examples:

```sql
CREATE USER tom;

GRANT SELECT ON test.* TO 'tom'@'%';
```

### CURRENT_USER

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

### DROP GROUP

Syntax:

```sql
DROP GROUP [IF EXISTS] 'group_name'
```

### DROP ROLE

Syntax:

```sql
DROP ROLE [IF EXISTS] 'role_name'
```

### DROP USER

Syntax:

```sql
DROP USER [IF EXISTS] user [, user] ...
```

### GRANT

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

### GRANT ROLE

Syntax:

```sql
GRANT ROLE 'role_name' TO 'group_name'
```

Examples:

```sql
GRANT ROLE 'full_backup_role' TO 'backup_admins_group';
```

### REVOKE

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

### REVOKE GROUP

Syntax:

```sql
REVOKE GROUP 'role_name' FROM user
```

Examples:

```sql
REVOKE GROUP 'backup_admins' FROM 'username'@'%';
```

### REVOKE ROLE

Syntax:

```sql
REVOKE ROLE 'role_name' FROM 'group_name'
```

Examples:

```sql
REVOKE ROLE ‘full_backups_role' FROM 'backup_admins';
```

## Encryption at Rest
