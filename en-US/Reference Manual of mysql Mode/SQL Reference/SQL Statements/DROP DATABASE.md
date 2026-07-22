## General Description

DROP DATABASE is used to delete the current database and the persistent data files it contains.

## Statement Definition

**drop database::=**

```ebnf
= DROP DATABASE [if exists] database_name.
```

***Example*** for Standalone Deployment Heap tables

```sql
DROP DATABASE [if exists] sales;

```
