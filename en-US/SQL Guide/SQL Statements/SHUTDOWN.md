## General Description

SHUTDOWN is used to shut down the currently running database.

Only users with the SYSDBA or SYSOPER roles can execute the SHUTDOWN operation.

Statement Definition
----

**shutdown::=**

```ebnf+diagram
syntax::= SHUTDOWN [NORMAL | IMMEDIATE | ABORT] [WAIT STANDBY]
```

### 1. NORMAL

The default shutdown option, which must wait for existing sessions to end before executing the shutdown; there will be no instance recovery after the database restarts.

### 2. IMMEDIATE

Shuts down the database as quickly as possible, without waiting for existing sessions to end, but will roll back uncommitted transactions. 

In this shutdown mode, all client sessions will be disconnected, and there will be no instance recovery after the database restarts.

### 3. ABORT

Shuts down the database as quickly as possible, without waiting for existing sessions to end, and does not roll back uncommitted transactions. 

In this shutdown mode, all client sessions will be disconnected, and instance recovery will take place after the database restarts.

### 4. WAIT STANDBY

This option indicates that before shutting down the database, logs must be synchronized to all standby databases, and it waits for these standby databases to apply.

***Example***

```sql
SHUTDOWN;

SHUTDOWN IMMEDIATE;

SHUTDOWN ABORT;

SHUTDOWN WAIT STANDBY;
```
