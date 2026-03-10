The DBMS_PART package provides a set of stored procedures/functions for cleaning up orphan key-value pairs in global indexes.

## CLEANUP\_GIDX\_JOB

```plsql
DBMS_PART.CLEANUP_GIDX_JOB (
   options   IN  VARCHAR DEFAULT 'CLEANUP_ORPHANS'
);
```

The CLEANUP_GIDX_JOB function is used to clean up all global indexes in the database that contain orphan key-value pairs, aiming to improve index performance and reduce space usage.

Index types that cannot perform coalesce cleanup, such as RTree indexes, will be skipped.

|Parameter |Description |
| :---- |:-----------|
| options | Cleanup options. CLEANUP_ORPHANS: indicates using coalesce cleanup only. COALESCE: indicates using coalesce cleanup. For details, refer to [COALESCE](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/ALTER INDEX.html#coalesce) |

***Example***

```plsql

exec DBMS_PART.CLEANUP_GIDX_JOB('CLEANUP_ORPHANS');
```
