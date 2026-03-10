## General Description

USE is used to specify the target database for subsequent operations in the current session. The specified database remains the default until the session ends or another USE statement is executed to point to a different database.

After specifying the database, if you need to access objects (such as tables or views) in other databases, their names must be specified in the format of `database_name.object_name`.

## Statement Definition

**use::=**

```ebnf+diagram
syntax::= USE database_name
```

***Example*** for Standalone Deployment Heap tables

```sql
USE sales;
```
