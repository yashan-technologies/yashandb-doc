General Description
----

SET TRANSACTION is used to set the isolation level of a transaction. The isolation levels of a transaction include:

*    read committed (READ COMMITTED): The default isolation level of the system, which allows only the reading of committed data versions. Each statement in a transaction supports statement-level read consistency.
*    serializable (SERIALIZABLE): The strictest isolation level, providing transaction-level read consistency and a complete mechanism for detecting write-write serialization conflicts.

The default transaction isolation level in ISC Distributed Cluster Deployment is READ COMMITTED, and users cannot execute this statement.

When performing transaction operations on a dblink remote database, only the READ COMMITTED isolation level is supported.

Statement Definition
----

**set transaction::=**

```ebnf+diagram
syntax::= SET TRANSACTION [ISOLATION LEVEL (READ COMMITTED|SERIALIZABLE)] [NAME comment_string]
```

### 1. READ COMMITTED

Sets the transaction isolation level to read committed.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### 2. SERIALIZABLE

Sets the transaction isolation level to serializable.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```
