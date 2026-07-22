This view displays the summary information of the database for the current instance.

|Field |Type |Description |
| --- | --- | --- |
| DATABASE_ID          | BIGINT   | Database ID                                                                                                                            |
| DATABASE_NAME        | VARCHAR(64) | Database Name                                                                                                                          |
| CREATE_TIME          | DATE     | Database Creation Time                                                                                                                 |
| LOG_MODE             | VARCHAR(16) | Whether archiving is enabled<br>* ARCHIVELOG: Archiving is enabled<br>* NOARCHIVELOG: Archiving is not enabled                         |
| OPEN_MODE            | VARCHAR(16) | Startup Mode<br>* READ_ONLY: Read-only mode<br>* READ_WRITE: Read-write mode<br>* MOUNTED: Mount status                               |
| PROTECTION_MODE      | VARCHAR(32) | Protection Mode<br>* MAXIMUM PERFORMANCE: maximize performance <br>* MAXIMUM AVAILABILITY: maximize availability <br>* MAXIMUM PROTECTION: maximize protection  |
| PROTECTION_LEVEL     | VARCHAR(32) | Protection Level<br>* UNPROTECTED: Database is not OPEN<br>* MAXIMUM PERFORMANCE: maximize performance is active<br>* MAXIMUM AVAILABILITY: maximize availability is active, and redo has been synchronized to standby database <br>* RESYNCHRONIZATION: maximize availability is active, but redo has not been synchronized to standby database <br>* MAXIMUM PROTECTION: maximize protection is active, and redo synchronization is normal<br>* SYNCHRONIZATION BLOCKING: maximize protection is active, but redo synchronization is abnormal, transaction commits will be blocked |
| DATABASE_ROLE        | VARCHAR(16) | Database Role<br>* PRIMARY: primary database <br>* STANDBY: physical standby database <br>* LOGICAL STANDBY: logical standby database  |
| BLOCK_SIZE           | INTEGER  | Database data block size (unit: bytes)                                                                                               |
| CURRENT_SCN          | BIGINT   | Current SCN of the database                                                                                                           |
| STATUS               | VARCHAR(32) | Database Status<br>* NORMAL: Normal<br>* NEED REPAIR: Standby database status is abnormal and needs to be rebuilt<br>* REDO MISMATCH: Some logs of the standby database do not match the primary database, needs to check and repair<br>* ABNORMAL: Abnormal status, write operations cannot be performed |
| RCY_POINT            | VARCHAR(32) | Log recovery point when the database starts, {resetid}-{asn}-{blockid}-{lfn}                                                              |
| FLUSH_POINT          | VARCHAR(32) | Current log flush point of the database, {resetid}-{asn}-{blockid}-{lfn}                                                                  |
| RESET_POINT          | VARCHAR(32) | Log reset point of the database, {resetid}-{asn}-{blockid}-{lfn}                                                                          |
| PLATFORM_NAME        | VARCHAR(256) | Operating System                                                                                                                       |
| HOST_NAME            | VARCHAR(256) | Server User Name                                                                                                                      |
| RESTORE_TIME         | TIMESTAMP(6) | SCN of the database restore                                                                                                            |
| SWITCHOVER_STATUS     | VARCHAR(32) | Status of Primary/Standby role switch<br>* NOT ALLOWED: Switch conditions are not met<br>* TO STANDBY: Database is ready to switch to standby role<br>* TO PRIMARY: Database is ready to switch to primary role<br>* WAIT PRIMARY DEMOTE: Switching process, waiting for primary database to complete demotion<br>* PROMOTING: Standby database is switching to primary database <br>* DEMOTING: Primary database is switching to standby database |
| SUPPLEMENTAL_LOG_DATA_MIN | VARCHAR(8) | Whether minimum supplemental logging is enabled<br>* YES: Enabled<br>* NO: Disabled <br>* IMPLICIT: Implicitly enabled                |
| SUPPLEMENTAL_LOG_DATA_PK | VARCHAR(8) | Whether PRIMARY KEY mode supplemental logging is enabled<br>* YES: Enabled<br>* NO: Disabled                                          |
| SUPPLEMENTAL_LOG_DATA_ALL | VARCHAR(8) | Whether ALL mode supplemental logging is enabled<br>* YES: Enabled<br>* NO: Disabled                                                 |
| SUPPLEMENTAL_LOG_TABLE_TYPE | VARCHAR(16) | Table types for database-level DML supplemental logging, with values in [HEAP, TAC, LSC]. If empty, it means no DML supplemental logging for all tables. |
| GUARD_STATUS         | VARCHAR(8) | Protect data from being changed<br>* ALL: Indicates that all users except SYS cannot change any data in the database<br>* NONE: Does not intercept changes to data. |
| DBID                 | BIGINT   | Database ID                                                                                                                            |
| NAME                 | VARCHAR(64) | Database Name                                                                                                                          |
| CREATED              | DATE     | Database Creation Time                                                                                                                 |
| RESETLOGS_CHANGE#   | BIGINT   | System modification sequence number (SCN) when open resetlogs is executed                                                             |
| FLASHBACK_ON         | VARCHAR(8) | Whether database flashback functionality is enabled<br/>* YES: Enabled<br/>* NO: Disabled                                       |
| CDB | VARCHAR(8) | Is this a CDB<br/>* YES<br/>* NO |
| RECOVERY\_REDO\_SIZE           | BIGINT   | Size of redo to be recovered when performing database recovery |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

