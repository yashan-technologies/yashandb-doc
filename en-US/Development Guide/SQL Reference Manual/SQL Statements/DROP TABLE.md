General Description
----

The DROP TABLE statement is used to remove table objects (including the indexes, constraints, triggers, etc., built on them). The data in the table is also deleted, and the data space occupied by the table and its indexes is fully released.

When the [Recycle Bin is enabled](../../../Database Administration/Fault Handling/Flashback/Recycle Bin Object Flashback/00Recycle Bin Object Flashback) (configuration parameter RECYCLEBIN_ENABLED is ON), executing a DROP statement to delete a HEAP table without specifying the PURGE keyword will move the table and its associated data (indexes, constraints, triggers, LOB columns, etc.) to the Recycle Bin (the table name is renamed to a system-generated string starting with BIN); deleting TAC tables, LSC tables, or external tables will always result in permanent deletion.

The DROP operation deletes all related definitions of the table object. If the table needs to be used again, it must be rebuilt as a new table.

The DROP operation cannot be rolled back, nor can the data prior to the operation be retrieved using [flashback_query_clause](SELECT). If the PURGE statement is specified, it cannot be restored from the recycle bin.

When it is necessary to DROP a parent table that has foreign key constraints defined by a child table, CASCADE CONSTRAINTS must be specified.

When it is necessary to DROP a source table that has been established as an AC, all ACs on that table must be dropped first before the table can be dropped.

Statement Definition
----

**drop table::=**

```ebnf
= DROP TABLE [IF EXISTS] [schema "."] table_name [CASCADE CONSTRAINTS] [PURGE].
```

### IF EXISTS

This statement is used to specify that errors for non-existent tables be ignored when dropping the table.

***Example***

```sql
--DROP a non-existent table area_drop
DROP TABLE IF EXISTS area_drop;
```

### CASCADE CONSTRAINTS

This statement applies only to HEAP tables and is used to specify that when a table has foreign key constraints defined by a child table, the foreign key constraints on the child table are deleted first before dropping the table.

***Example*** for Heap tables

```sql
--The foreign key constraint defined on the child table branches of area is deleted
DROP TABLE area CASCADE CONSTRAINTS;
```

### PURGE

This statement is used to specify that the table is to be permanently deleted rather than sent to the recycle bin (RecycleBin).

This statement cannot be used in ISC Distributed Cluster Deployment.

If PURGE is explicitly specified, the dropped table object and data will not enter the recycle bin but will be permanently deleted and cannot be recovered.

***Example*** for Heap tables

```sql
DROP TABLE finance_info PURGE;
```
