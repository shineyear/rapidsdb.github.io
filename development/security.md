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

## Encryption at Rest
