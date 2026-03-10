General Description
----

DROP FUNCTION is used to delete an existing [UDF] (..\..\PL Reference Manual\PL Objects\User-Defined Functions).

Ordinary users can only delete the UDFs they have created.

Deleting this function will cause a compile error when the function is referenced by other procedure bodies.

Statement Definition
----

**drop function::=**

```ebnf+diagram
syntax::= DROP FUNCTION [IF EXISTS] [schema "."] function_name
```

### 1. IF EXISTS

This clause is used to specify whether to check for the existence of the UDF before dropping it. If omitted, no check will be performed; in this case, if the UDF to be deleted does not exist, the system will prompt an error.

***Example*** for Standalone Deployment and YAC Deployment

```sql
DROP FUNCTION IF EXISTS ya_func; 
```
