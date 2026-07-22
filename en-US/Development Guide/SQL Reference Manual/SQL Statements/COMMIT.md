General Description
----

COMMIT is used to submit a transaction.

In Standalone Deployment, COMMIT is equivalent to COMMIT WORK WRITE WAIT IMMEDIATE.

In ISC Distributed Cluster Deployment, COMMIT is equivalent to COMMIT WORK WRITE WAIT.

Before executing the COMMIT operation, any modifications made by the user during the transaction process are only visible to themselves; other users cannot see them, and modifications can be reverted by a rollback.

When executing the COMMIT operation, the system persists the user's modifications (writing redo logs to disk, triggering disk I/O), clears all SAVEPOINTs in the session, and releases all locks held by the current transaction, thus ending the transaction.

After executing the COMMIT operation, all users can see the modified data, and modifications cannot be reverted by a rollback.

The system automatically invokes the COMMIT statement at the following points:

*   Before the start of DDL operations.
*   After successful completion of DDL operations.
*   When the session exits normally.

Statement Definition
----

**commit::=**

```ebnf
= COMMIT [WORK] (([WRITE [IMMEDIATE|BATCH] [WAIT|NOWAIT]]) | FORCE GTID["," SCN]).
```

### WORK

This clause is for compatibility with standard SQL syntax and has no actual meaning.

### FORCE

This clause is used to force the submission of a specified transaction in distributed transactions.

GTID: Obtained from the [GV$2PC_PENDING view](../../../Reference Manual/System Views/Dynamic Performance Views/GV$2PC_PENDING). This parameter is mandatory for the FORCE statement.

SCN: Allocates an SCN number for this submission; this parameter can be omitted, in which case the current SCN number will be used.

***Example*** for ISC Distributed Cluster Deployment
```sql
-- This GTID is just an example; it should actually be obtained from the GV$2PC_PENDING view.
COMMIT FORCE 1234567;

-- This SCN is just an example; it should actually be greater than the SCN of the corresponding transaction in the GV$2PC_PENDING view.
COMMIT FORCE 1234567,12345;
```

### WRITE

This clause specifies how the redo logs are written to disk, with the default being WRITE WAIT IMMEDIATE.

In ISC Distributed Cluster Deployment, regardless of whether the WRITE clause is used or any combination of WRITE clauses is adopted, the result is equivalent to COMMIT WORK WRITE WAIT IMMEDIATE.

#### IMMEDIATE|BATCH

Specifies when the redo logs are written to disk; IMMEDIATE indicates immediate writing to disk, while BATCH indicates that the system accumulates redo data that need to be written until reaching a certain quantity before writing them to disk all at once.

The delay in accumulation for writing redo data in BATCH mode can be viewed through the BATCH_COMMIT_DELAY field in the v$redostat view.

#### WAIT|NOWAIT

Specifies whether to wait for the write operation; WAIT means COMMIT will successfully return only after the redo logs are successfully written to disk, while NOWAIT means that COMMIT will return successfully immediately, without guaranteeing that redo logs are successfully written to disk.

> **Note**: 
>
> When the configuration parameter COMMIT_WAIT is set to WAIT_FORCE, the system will enforce waiting even if NOWAIT is specified.

***Example***

```sql
COMMIT WRITE IMMEDIATE NOWAIT;
```
