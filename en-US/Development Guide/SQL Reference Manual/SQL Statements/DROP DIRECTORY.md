General Description
----

DROP DIRECTORY is used to delete a data directory object. The user executing this statement must have the drop any directory privilege.

Statement Definition
----

**drop directory::=**

```ebnf
= DROP DIRECTORY directory_name.
```

### directory_name

This clause is used to specify the name of the directory to be deleted and cannot be omitted.

***Example***

```sql
DROP DIRECTORY dir;
```
