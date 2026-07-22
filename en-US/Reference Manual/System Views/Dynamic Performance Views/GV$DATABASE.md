In Standalone/YAC/Distributed Cluster Deployment, this view displays the summary information of the current instance's database and the instance ID.

In ISC Distributed Cluster Deployment, this view shows the MN instance's database summary information on the CN, and displays the current instance's database summary information on the MN or DN.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| DATABASE_ID | BIGINT | Database ID |
| DATABASE_NAME | VARCHAR(64) | Database Name |
| CREATE_TIME | DATE | Creation Time |
| LOG_MODE | VARCHAR(16) | Whether archiving is enabled<br>\*   ARCHIVELOG: Archiving enabled<br>\*   NOARCHIVELOG: Archiving disabled |
| OPEN_MODE | VARCHAR(16) | Startup Mode<br>\*   READ_ONLY: Read-only mode<br>\*   READ_WRITE: Read-write mode<br>\*   MOUNTED: Mounted state |
| PROTECTION_MODE | VARCHAR(32) | Protection Mode<br>\*   MAXIMUM PERFORMANCE: Maximize performance <br>\*   MAXIMUM AVAILABILITY: Maximize availability <br>\*   MAXIMUM PROTECTION: Maximize protection  |
| PROTECTION_LEVEL | VARCHAR(32) | Protection Level<br>\*   UNPROTECTED: Database is not OPEN<br>\*   MAXIMUM PERFORMANCE: Maximize performance is active<br>\*   MAXIMUM AVAILABILITY: Maximize availability is active, and redo is synchronized to standby database <br>\*   RESYNCHRONIZATION: Maximize availability is active, but redo is not synchronized to standby database <br>\*   MAXIMUM PROTECTION: Maximize protection is active, and redo is synchronized properly<br>\*   SYNCHRONIZATION BLOCKING: Maximize protection is active, but redo synchronization is abnormal, transaction commits will be blocked |
| DATABASE_ROLE | VARCHAR(16) | Database Role<br>\*   PRIMARY: Primary database <br>\*   STANDBY: Physical standby database <br>\*   LOGICAL STANDBY: Logical standby database  |
| BLOCK_SIZE | INTEGER | Database data block size (in bytes) |
| CURRENT_SCN | BIGINT | Current SCN of the database |
| STATUS | VARCHAR(32) | Database Status<br>\*   NORMAL: Normal<br>\*   NEED REPAIR: Standby database status is abnormal, needs to be rebuilt<br>\*   REDO MISMATCH: Standby database has some logs that do not match the primary database, requires inspection and repair<br/>\*   ABNORMAL: Abnormal status, write operations cannot be performed |
| RCY_POINT | VARCHAR(32) | Log recovery point at database startup, formatted as {resetid}-{asn}-{blockid}-{lfn} |
| FLUSH_POINT | VARCHAR(32) | Current log flush point of the database,{resetid}-{asn}-{blockid}-{lfn}  |
| RESET_POINT | VARCHAR(32) | Database log reset point,{resetid}-{asn}-{blockid}-{lfn}  |
| PLATFORM_NAME | VARCHAR(256) | Operating System |
| HOST_NAME | VARCHAR(256) | Primary database username |
| RESTORE_TIME | TIMESTAMP(6) | SCN of the database restore  |
| SWITCHOVER_STATUS | VARCHAR(32) | Status of Primary/standby role switching<br>\*   NOT ALLOWED: Switching conditions not met<br>\*   TO STANDBY: Database is ready to switch to standby role<br>\*   TO PRIMARY: Database is ready to switch to primary role<br>\*   WAIT PRIMARY DEMOTE: Switching in progress, waiting for primary database to complete demotion<br>\*   PROMOTING: Standby database is switching to primary database <br>\*   DEMOTING: Primary database is switching to standby database   |
| SUPPLEMENTAL_LOG_DATA_MIN | VARCHAR(8) | Whether minimal supplemental logging is enabled<br>\*   YES: Enabled<br>\*   NO: Disabled <br>\*   IMPLICIT: Implicitly enabled |
| SUPPLEMENTAL_LOG_DATA_PK | VARCHAR(8) | Whether PRIMARY KEY mode supplemental logging is enabled<br>\*   YES: Enabled<br>\*   NO: Disabled |
| SUPPLEMENTAL_LOG_DATA_ALL | VARCHAR(8) | Whether ALL mode supplemental logging is enabled<br>\*   YES: Enabled<br>\*   NO: Disabled |
| SUPPLEMENTAL_LOG_TABLE_TYPE | VARCHAR(16) | Table type for database-level DML supplemental logging to take effect, valid values are [HEAP, TAC, LSC]. If empty, it means all table DML does not log supplemental logging. |
| GUARD_STATUS | VARCHAR(8) | Protect data from being altered<br>\*   ALL: Means all users except SYS cannot change any data in the database<br>\*   NONE: No interception of data changes. |
| DBID | BIGINT | Database ID |
| NAME | VARCHAR(64) | Database Name |
| CREATED | DATE | Creation Time |
| RESETLOGS_CHANGE# | BIGINT | System modification sequence number (SCN) at open resetlogs |
| FLASHBACK_ON | VARCHAR(8) | Whether the entire database flashback functionality is enabled <br/>*  YES: Enabled<br/>*  NO: Disabled |
| HAS_GEK  | VARCHAR(8) | Whether there is a global key<br/>* YES: Yes<br/>* NO: No |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

