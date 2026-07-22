General Description
----

CREATE DIRECTORY is used to create a new directory object. All users with the create any directory privilege can execute this statement to create a directory object, but the directory object is always owned by the sys user.

Statement Definition
----

**create directory::=**

```ebnf
= CREATE [OR REPLACE] DIRECTORY directory_name AS 'path_name'.
```

### directory_name

This statement is used to specify the name of the directory to be created. It is required and must comply with YashanDB's [object naming convention](../Basic SQL Elements/Identifiers).

### path_name

This statement is used to specify the location of the directory to be created. It supports specifying local file paths and YFS file paths (refer to [YFS File Management](../../../Database Administration/Storage Management/YFS Management/File Management) for YFS file paths).

This statement must meet the following rules:

- Length limit is 4000.
- It cannot contain parent directories (..).
- The existence of the directory is not checked when creating it.

***Example***

```sql
CREATE DIRECTORY dir AS '/data/yashan';
```
