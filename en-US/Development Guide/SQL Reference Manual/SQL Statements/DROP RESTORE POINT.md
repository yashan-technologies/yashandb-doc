General Description
----

DROP RESTORE POINT is used to delete an existing restore point.

This statement is not applicable to ISC Distributed Cluster Deployment.

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), the restore points on the CDB root and each PDB are independent of each other. You must connect to the target container to perform restore point-related management operations, and such operations will only take effect on that specific container.


The user executing this statement must have FLASHBACK ANY TABLE or higher privileges. To delete a [permanent restore point](./CREATE RESTORE POINT), the SYS user must be used.

Before deleting a restore point, ensure that the database has the [database flashback](ALTER DATABASE.md#flashbackdatabaseclauses) functionality enabled and is currently in MOUNT or OPEN state.

Statement Definition
----

**drop restore point::=**

```ebnf
= DROP RESTORE POINT restore_point_name.
```

### restore\_point\_name

This statement is used to specify the name of the restore point to be deleted. Information about existing restore points can be obtained from the V$RESTORE_POINT view.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
DROP RESTORE POINT p202412011100;
```
