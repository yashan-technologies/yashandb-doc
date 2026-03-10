General Description
----

The LOCK TABLE statement is used to apply table locks to tables, views, or materialized views in a specified mode. The user executing this operation must have the appropriate privileges on the objects being operated on:

- The object belongs to the current user.
- The object does not belong to the current user:

    - If the object belongs to the sys user, the current user must have access to the view and possess any of the INSERT, DELETE, or UPDATE object privileges on the table.

    - If the object belongs to a non-sys user, the current user must have access to the view and possess any object privilege other than READ on the table.

LOCK TABLE is not applicable for ISC Distributed Cluster Deployment.

Statement Definition
----

**lock table::=**

```ebnf+diagram
syntax::= LOCK TABLE ([ schema"." ](table | view)){","([ schema"." ](table | view))} IN lockmode MODE[NOWAIT|(WAIT integer)]";"
```

### 1. lockmode

This field is used to specify the type of table lock.

**SHARE**

A shared table lock allows other transactions to acquire shared locks on the table, but does not permit any DDL operations on the table by other transactions.

**EXCLUSIVE**

An exclusive table lock only allows other transactions to read the table, prohibiting any DML operations or any lock acquisitions on the table by other transactions.

### 2. waitmode

This field is used to specify whether the locking operation should wait and the conditions for the timeout judgment. During the waiting period, the current session does not have control (i.e., new statements cannot be input).

If neither WAIT nor NOWAIT is specified, the database will wait indefinitely until it can lock the table and then restore session control.

**NOWAIT**

Specifies the NOWAIT mode; the database will immediately return an error message if it cannot acquire the lock.

**WAIT**

Specifies the WAIT mode; the database will take an integer value as the maximum timeout for waiting. If the wait time exceeds the maximum limit and the lock cannot be acquired, an error message will be returned.

The integer must be specified as a whole number, with a range of [0,2147483647], measured in seconds.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Lock the table
LOCK TABLE area,department IN SHARE MODE NOWAIT;

-- Lock the view
LOCK TABLE v_area IN EXCLUSIVE MODE WAIT 100;
```
