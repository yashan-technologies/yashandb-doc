General Description
----

ROLLBACK is used to roll back a transaction. The following are the rollback rules when no SAVEPOINT is specified:

*   Before executing the ROLLBACK operation, users can see their modified data in real-time, while other users cannot see it.
*   When the ROLLBACK operation is executed, the system will restore the data in the Data Buffer to its previous values, clear all SAVEPOINTs in the transaction's session, release all locks at the transaction level, and end the current transaction.
*   After executing the ROLLBACK operation, all users can only see the data before modification.

In YashanDB, ROLLBACK is automatically invoked in case of any exceptions in the system.

Statement Definition
----

**rollback::=**

```ebnf+diagram
syntax::= ROLLBACK [WORK] (([TO [SAVEPOINT] savepoint_name]) | FORCE GTID)
```

### 1. WORK

This statement is used for compatibility with standard SQL syntax and has no real meaning.

### 2. FORCE

This statement is used to forcefully roll back a specified transaction in a distributed transaction.

GTID: Obtained from the [GV$2PC_PENDING view](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/GV$2PC_PENDING), this parameter is mandatory for the FORCE statement.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- The GTID provided in this example is for demonstration only; it should be obtained from the GV$2PC_PENDING view.
ROLLBACK FORCE 1234567;
```

### 3. TO SAVEPOINT

This statement is used to roll back operations after the specified savepoint. When this statement is executed:

*   The system will restore the data in the Data Buffer to its value at the savepoint.
*   All SAVEPOINTs after the specified savepoint will be cleared.
*   The transaction will not end.

***Example***

```sql
-- Roll back the entire transaction
ROLLBACK;

-- Roll back to the specified savepoint
ROLLBACK TO SAVEPOINT sa_area_1;
```
