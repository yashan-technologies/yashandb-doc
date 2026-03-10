## General Description

The CREATE DATABASE statement is used to create a new database. This statement can only be executed when the database is started to the open stage and switched to mysql mode.

## Statement Definition

**create database::=**

```ebnf+diagram
syntax::= CREATE DATABASE [IF NOT EXISTS] database_name [CHARACTER SET charset_name] [COLLATE collation_name] 
```

***Example*** for  Heap tables

```sql
CREATE DATABASE IF NOT EXISTS sales DEFAULT CHARACTER SET `utf8` DEFAULT collate `utf8mb4_general_ci`;
```
