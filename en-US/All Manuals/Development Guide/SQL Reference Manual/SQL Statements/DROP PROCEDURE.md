General Description
----

DROP PROCEDURE is used to delete an existing [stored procedure](../../PL Reference Manual/PL Objects/Stored Procedures).

Ordinary users can only delete stored procedures they have created.

Deleting a stored procedure that is referenced by other procedure bodies will result in a compilation error when attempting to execute those procedure bodies.

Statement Definition
----

**drop procedure::=**

```ebnf+diagram
syntax::= DROP PROCEDURE [IF EXISTS] [schema "."] procedure_name
```

### 1. IF EXISTS

This clause is used to specify that the system should check whether the stored procedure exists before attempting to DROP it. If omitted, the system will not perform this check, and an error will be prompted if the stored procedure to be deleted does not exist.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DROP PROCEDURE IF EXISTS ya_proc;
```
