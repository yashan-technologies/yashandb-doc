## General Description

ALTER DATABASE is used to modify the properties of a database.

Statement Definition
----

**alter database::=**

```ebnf+diagram
syntax::= ALTER DATABASE database_name [DEFAULT] [CHARACTER SET charset_name] [DEFAULT]
```

***Example*** for  Heap tables

```sql
ALTER DATABASE sales DEFAULT CHARACTER SET 'utf8mb4';

```
