General Description
----

DROP RESTORE POINT is used to delete an existing restore point.

This statement is only applicable to Standalone Deployment, and the user executing this statement must have FLASHBACK ANY TABLE privilege or higher.

Only the SYS user can delete a [permanent restore point](CREATE RESTORE POINT).

Before deleting a restore point, ensure that the database has the [full database flashback](ALTER DATABASE.html#flashbackdatabaseclauses) functionality enabled and is currently in MOUNT or OPEN state.

Statement Definition
----

**drop restore point::=**

```ebnf+diagram
syntax::= DROP RESTORE POINT restore_point_name
```

### 1. restore\_point\_name

This statement is used to specify the name of the restore point to be deleted. Information about existing restore points can be obtained from the V$RESTORE_POINT view.

***Example*** for Standalone Deployment

```sql
DROP RESTORE POINT p202412011100;
```
