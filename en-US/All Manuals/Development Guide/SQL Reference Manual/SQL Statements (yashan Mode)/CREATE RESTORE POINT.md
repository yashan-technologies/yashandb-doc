General Description
----

CREATE RESTORE POINT is used to create a restore point, which can serve as a target for a database flashback.

This statement is applicable only to Standalone Deployment, and the user executing this statement must have FLASHBACK ANY TABLE privilege or higher.

Before creating a restore point, ensure that the database has the [flashback database](ALTER DATABASE.html#flashbackdatabaseclauses) functionality enabled and is currently in MOUNT or OPEN state.

Statement Definition
----

**create restore point::=**

```ebnf+diagram
syntax::= CREATE RESTORE POINT restore_point_name [AS OF (SCN scn|TIMESTAMP timestamp)] [GUARANTEE FLASHBACK DATABASE]
```

### 1. restore\_point\_name

This statement specifies the name of the restore point to be created, which is mandatory. The name must be globally unique and comply with YashanDB's [naming conventions](../Basic SQL Elements/Identifiers).

### 2. AS OF SCN|TIME

This statement specifies the timeline corresponding to the restore point to be created. If not specified, the current timeline when the statement is executed will be used as default.

The specified timeline cannot be earlier than the oldest timeline shown in the V$FLASHBACK_DATABASE_LOG view.

### 3. GUARANTEE FLASHBACK DATABASE

The presence of this statement indicates that the created restore point is a permanent restore point. If omitted, it defaults to a normal (non-permanent) restore point. Only the SYS user can create permanent restore points.

During database operation or when executing database flashback operations, maintenance of resources related to database flashback will occur, which cleans up all normal restore points. If designated as a permanent restore point, it will not be automatically cleaned up.

When permanent restore points exist, the database cannot disable flashback functionality until all permanent restore points are manually [dropped](DROP RESTORE POINT).

***Example*** for Standalone Deployment

```sql
-- Enable database flashback
ALTER DATABASE FLASHBACK ON;

-- Create a permanent restore point with specified scn
CREATE RESTORE POINT p20241201 AS OF SCN 673515539901313024 GUARANTEE FLASHBACK DATABASE;

-- Create a normal restore point with specified scn
CREATE RESTORE POINT p202412011000 AS OF SCN 673515539901313024;

-- Create a normal restore point without specifying scn
CREATE RESTORE POINT p202412011100;
```
