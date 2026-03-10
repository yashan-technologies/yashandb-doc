The UNDO tablespace is used by YashanDB to create and manage rollback (undo database changes) information, which includes records of transactional behavior, primarily before the transaction is committed, collectively referred to as undo.

Undo records are used for:

-   Executing the ROLLBACK statement to roll back transactions
-   Restoring the database
-   Providing read consistency
-   Analyzing data from earlier points in time using flashback queries
-   Recovering from logical corruption using flashback functionality

**Setting the Minimum Undo Retention Period**

YashanDB provides an automated mechanism called Automatic Undo Management for managing undo information and space.

When Automatic Undo Management is enabled, there is always a current undo retention period, which is the minimum time YashanDB tries to retain the information before overwriting the old undo information.

The minimum undo retention period is specified by the UNDO_RETENTION parameter (in seconds):

```sql
ALTER SYSTEM SET UNDO_RETENTION = 2400;
```

**Adding Data File to UNDO Tablespace**

> **Note**: 
>
> When specifying the data file path, absolute or relative paths can be used in Standalone Deployment, absolute paths must be specified for YAC Deployment and can only be YFS paths, and only relative paths are permitted in ISC Distributed Cluster Deployment. 

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE UNDO ADD DATAFILE '?/dbfiles/undo02' SIZE 10M AUTOEXTEND ON;
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE undo0 ADD DATAFILE '+DG0/dbfiles/undo02' SIZE 10M AUTOEXTEND ON;
```

**Adjusting Data File Size for UNDO Tablespace**

This functionality is not available to ISC Distributed Cluster Deployment, and the data file in the UNDO tablespace can only be increased and cannot be decreased.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/undo02' RESIZE 20M;
```

***Example*** for YAC Deployment

```sql
ALTER DATABASE DATAFILE '+DG0/dbfiles/undo02' RESIZE 20G;
```

**Dropping UNDO Tablespace**

This functionality is not available in ISC Distributed Cluster Deployment.

***Example*** for Standalone Deployment and YAC Deployment

```sql
DROP TABLESPACE undo0 INCLUDING CONTENTS AND DATAFILES;
```

>  **Note**: 
>
> The UNDO tablespace can only be dropped when it is not currently being used by any instances.

**Viewing UNDO Related Statistics**

***Example***

```sql
SELECT ID,BLK_REUSE,BALANCE_TIME,BALANCE,BALANCE_BLK,RECYCLE_TIME,RECYCLE_LIST,RECYCLE_LIST_BLK FROM V$UNDOSTAT;

  ID    BLK_REUSE BALANCE_TIME          BALANCE  BALANCE_BLK RECYCLE_TIME      RECYCLE_LIST RECYCLE_LIST_BLK
---- ------------ ---------------- ------------ ------------ ----------------- ------------ ----------------
   0            9 2022-07-04                425          112 1970-01-01                   0                0
   1            6 2022-07-04                425          216 1970-01-01                   0                0
   2            8 2022-07-04                425          326 1970-01-01                   0                0
   3            9 2022-07-04                425          437 1970-01-01                   0                0
   4            9 2022-07-04                425          564 1970-01-01                   0                0
```

**Viewing UNDO SEGMENT Information**

***Example***

```sql
SELECT ID,USED_TIME,FIRST_UBAFIL,FIRST_UBABLK,LAST_UBAFIL,LAST_UBABLK,UFB_COUNT,FIRST_UFBFIL,FIRST_UFBBLK FROM V$UNDO_SEGMENTS;

 ID USED_TIME      FIRST_UBAFIL FIRST_UBABLK  LAST_UBAFIL  LAST_UBABLK    UFB_COUNT FIRST_UFBFIL FIRST_UFBBLK
--- -------------- ------------ ------------ ------------ ------------ ------------ ------------ ------------
  0 2022-07-04                0         7012            0         7012           15            0         7013
  1 2022-07-04                0         6472            0         6472            7            0         6473
  2 2022-07-04                0         6510            0         6510            1            0         6511
  3 2022-07-04                0         6883            0         6883            0            0         6885
  4 2022-07-04                0         6527            0         6527            0            0         6528
```
