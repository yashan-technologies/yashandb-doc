## General Description

SHUTDOWN is used to shut down the currently running database.

Only users with the SYSDBA or SYSOPER roles can execute the SHUTDOWN operation.

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), if this statement is used while connected to the CDB root, it indicates performing global operations, covering both the CDB root and all PDBs. If this statement is used while directly connected to a specific PDB, it indicates operating on that PDB.


Statement Definition
----

**shutdown::=**

```ebnf
= SHUTDOWN [NORMAL | IMMEDIATE | ABORT] [WAIT STANDBY].
```

### NORMAL

The default shutdown option, which must wait for existing sessions to end before executing the shutdown; there will be no instance recovery after the database restarts.

### IMMEDIATE

Shuts down the database as quickly as possible, without waiting for existing sessions to end, but will roll back uncommitted transactions. 

In this shutdown mode, all client sessions will be disconnected, and there will be no instance recovery after the database restarts.

### ABORT

Shuts down the database as quickly as possible, without waiting for existing sessions to end, and does not roll back uncommitted transactions. 

In this shutdown mode, all client sessions will be disconnected, and instance recovery will take place after the database restarts.

### WAIT STANDBY

This option indicates that before shutting down the database, logs must be synchronized to all standby databases, and it waits for these standby databases to apply.

***Example***

```sql
SHUTDOWN;

SHUTDOWN IMMEDIATE;

SHUTDOWN ABORT;

SHUTDOWN WAIT STANDBY;
```
