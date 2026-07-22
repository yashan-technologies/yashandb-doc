General Description
----

DROP FUNCTION is used to delete an existing [UDF] (..\..\PL Reference Manual\PL Objects\User-Defined Functions).

Regular users can only delete the UDFs they have created.

Deleting this function will cause a compile error when the function is referenced by other procedure bodies.

Statement Definition
----

**drop function::=**

```ebnf
= DROP FUNCTION [IF EXISTS] [schema "."] function_name.
```

### IF EXISTS

This clause is used to specify whether to check for the existence of the UDF before dropping it. If omitted, no check will be performed; in this case, if the UDF to be deleted does not exist, the system will prompt an error.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
DROP FUNCTION IF EXISTS ya_func; 
```
