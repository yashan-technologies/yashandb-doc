General Description
----

RECOVER DATABASE is used to restore the database to a specified point in time from backup sets and archive log files / online logs, where:

   RECOVER DATABASE (FROM) = complete recovery, meaning to apply all redo as much as possible to restore to the latest state.

   RECOVER DATABASE UNTIL ... = incomplete recovery, meaning to restore to the specified target point and then stop.

RECOVER can only be executed when the database is in MOUNT state.

When restoring the database from a backup set, the database must first be restored to the state of the backup set by executing [RESTORE](RESTORE DATABASE) before performing RECOVER. When performing PITR recovery, the RECOVER operation requires that the archive log files generated after the backup set creation time and before the specified point in time must exist.

After performing an incomplete recovery, the ALTER DATABASE OPEN RESETLOGS statement must be executed to open the database and reset the redo timeline.

This statement is not applicable to ISC Distributed Cluster Deployment.

For detailed operational descriptions of backup recovery, please refer to [Backup and Recovery](../../../Database Administration/Backup and Recovery/00Backup and Recovery).

Statement Definition
----

**recover database::=**

```ebnf+diagram
syntax::= RECOVER DATABASE [(UNTIL (TIME date|SCN scn|CANCEL))|(FROM SEQUENCE integer)]
```

### 1. UNTIL TIME 

This statement is used to restore the database to the specified point in time, where the date format must be consistent with the format defined by the DATE_FORMAT parameter.

The specified point in time must be after the backup set creation time.

> **Note**: 
>
> The minimum point in time is the point that ensures the database is restored to a consistent state, which corresponds to the time of the SCN when datafile backup was completed and the archive backup started, slightly less than the backup completion time.
>
> If there are not enough archive log files, or if the specified point in time is too late, the database will report an error after the apply is complete even if the specified point is not reached. Users can check the actual apply progress by querying V$DATABASE's current_scn. If there are available copies of archive log files, they can be registered for archiving, and the restore operation can be performed again. If there are no extra archives, the current point in time is the only option, and the resetlogs operation should be continued.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Start the database in NOMOUNT state, then perform restore operation
RESTORE DATABASE FROM 'backup';
 
-- After RESTORE operation is successful, the database starts in MOUNT state
RECOVER DATABASE UNTIL TIME TO_DATE('2021-11-25 18:55:00','yyyy-mm-dd hh24:mi:ss');
 
-- Open the database
ALTER DATABASE OPEN RESETLOGS;
```

### 2. UNTIL SCN 

This statement is used to restore the database to the specified SCN number. The specified SCN number must be greater than the SCN number recorded in the backup set.

> **Note**: 
>
> The minimum SCN is the SCN that ensures the database is restored to a consistent state, which is the SCN when datafile backup was completed and the archive backup started, possibly slightly less than the SCN at backup completion.
>
> If there are not enough archive log files, or if the specified SCN is too late, the database will report an error after the apply is complete even if the specified SCN is not reached. Users can check the actual apply progress by querying V$DATABASE's current_scn. If there are available copies of archive log files, they can be registered for archiving, and the restore operation can be performed again. If there are no extra archives, the current point in time is the only option, and the resetlogs operation should be continued.

### 3. UNTIL CANCEL 

This statement can be used to force the database to start in the event of a normal startup failure, where it cannot be restored to a consistent state and no other recovery methods are available. After executing this statement, the OPEN command must be executed with RESETLOGS.

> **Warning**: 
>
> This statement is a high-risk operation. Forcing the database to start after using this statement may lead to various unpredictable failures, such as crashes, transaction inconsistencies, etc.
>
> When the database cannot be restored to a consistent state, other recovery methods should be prioritized before considering this recovery method.
>
> It is recommended to export necessary data and rebuild the database after forcing the database to start using this statement. Do not continue using the original database to prevent potential unknown failures.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- First, start the database in MOUNT, then execute the following statement to force start
RECOVER DATABASE UNTIL CANCEL;

-- Open the database
ALTER DATABASE OPEN RESETLOGS;
```

### 4. FROM SEQUENCE 

This statement is used to restore the database from the specified log sequence number. The specified log sequence number must not be greater than the recovery starting point of the current database (corresponding to V$DATABASE's RCY_POINT), and it must ensure that the logs following the specified sequence number exist.