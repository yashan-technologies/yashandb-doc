General Description
----

DROP DIRECTORY is used to delete a data directory object. The user executing this statement must have the drop any directory privilege.

Statement Definition
----

**drop directory::=**

```ebnf+diagram
syntax::= DROP DIRECTORY directory_name
```

### 1. directory\_name

This clause is used to specify the name of the directory to be deleted and cannot be omitted.

***Example***

```sql
DROP DIRECTORY dir;
```
