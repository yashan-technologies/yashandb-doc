General Description
----

RELEASE SAVEPOINT is used to remove a specific savepoint from a set of savepoints within the current transaction. After deletion, it will no longer be possible to roll back to that savepoint.

Statement Definition
----

**release savepoint::=**

```ebnf+diagram
syntax::= RELEASE SAVEPOINT savepoint_name
```

***Example***

```sql
-- Set savepoint
SAVEPOINT sp_1;

-- Delete savepoint
RELEASE SAVEPOINT sp_1;
```
