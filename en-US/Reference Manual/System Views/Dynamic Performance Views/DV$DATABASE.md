This view displays the summary information of databases from all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | INTEGER   | Group ID                                                                                          |
| GROUP_NODE_ID       | INTEGER   | Node ID within the group                                                                          |
| DATABASE_ID         | BIGINT    | Database ID                                                                                        |
| DATABASE_NAME       | VARCHAR(64) | Database Name                                                                                    |
| CREATE_TIME         | DATE      | Database Creation Time                                                                            |
| LOG_MODE            | VARCHAR(16) | Archive Mode<br>\*   ARCHIVELOG: Archive enabled<br>\*   NOARCHIVELOG: Archive not enabled      |
| OPEN_MODE           | VARCHAR(16) | Open Mode<br>\*   READ_ONLY: Read-only mode<br>\*   READ_WRITE: Read-write mode                |
| PROTECTION_MODE     | VARCHAR(32) | Protection Mode<br>\*   MAXIMUM PERFORMANCE: maximize performance <br>\*   MAXIMUM AVAILABILITY: maximize availability <br>\*   MAXIMUM PROTECTION: maximize protection  |
| PROTECTION_LEVEL     | VARCHAR(32) | Protection Level<br>\*   UNPROTECTED: Database not OPEN<br>\*   MAXIMUM PERFORMANCE: maximize performance active<br>\*   MAXIMUM AVAILABILITY: maximize availability active, and redo is synchronized to standby database <br>\*   RESYNCHRONIZATION: maximize availability active, but redo is not synchronized to standby database <br>\*   MAXIMUM PROTECTION: maximize protection active, and redo synchronization normal<br>\*   SYNCHRONIZATION BLOCKING: maximize protection active, but redo synchronization not normal, transaction commit will be blocked |
| DATABASE_ROLE       | VARCHAR(16) | Database Role<br>\*   PRIMARY: primary database <br>\*   STANDBY: physical standby database <br>\*   LOGICAL STANDBY: logical standby database  |
| BLOCK_SIZE          | INTEGER   | Database data block size (unit: bytes)                                                          |
| CURRENT_SCN         | BIGINT    | Current SCN of the database                                                                       |
| STATUS              | VARCHAR(32) | Database Status<br>\*   NORMAL: Normal<br>\*   NEED REPAIR: Standby database status abnormal, needs to be rebuilt<br>\*   REDO MISMATCH: Standby database has some logs not matching with primary database, needs to be checked and repaired<br/>\*   ABNORMAL: Abnormal status, unable to perform write operations |
| RCY_POINT           | VARCHAR(32) | Log recovery point at database startup, {resetid}-{asn}-{blockid}-{lfn}                           |
| FLUSH_POINT         | VARCHAR(32) | Current log flush point of the database, {resetid}-{asn}-{blockid}-{lfn}                          |
| RESET_POINT         | VARCHAR(32) | Log reset point of the database, {resetid}-{asn}-{blockid}-{lfn}                                  |
| PLATFORM_NAME       | VARCHAR(256) | Operating System                                                                                 |
| HOST_NAME           | VARCHAR(256) | Server Username                                                                                  |
| RESTORE_TIME        | TIMESTAMP(6) | SCN of the database restore                                                                       |
| SWITCHOVER_STATUS    | VARCHAR(32) | Status of Primary/Standby role switch<br>\*   NOT ALLOWED: Switch conditions not met<br>\*   TO STANDBY: Database is ready to switch to standby role<br>\*   TO PRIMARY: Database is ready to switch to primary role<br>\*   WAIT PRIMARY DEMOTE: Switching in progress, waiting for primary database to complete demotion<br>\*   PROMOTING: Standby database is switching to primary database <br>\*   DEMOTING: Primary database is switching to standby database   |
| SUPPLEMENTAL_LOG_DATA_MIN | VARCHAR(8) | Whether minimum supplemental logging is enabled<br>\*   YES: Enabled<br>\*   NO: Disabled <br>\*   IMPLICIT: Implicitly enabled |
| SUPPLEMENTAL_LOG_DATA_PK | VARCHAR(8) | Whether PRIMARY KEY mode supplemental logging is enabled<br>\*   YES: Enabled<br>\*   NO: Disabled |
| SUPPLEMENTAL_LOG_DATA_ALL | VARCHAR(8) | Whether ALL mode supplemental logging is enabled<br>\*   YES: Enabled<br>\*   NO: Disabled     |
| SUPPLEMENTAL_LOG_TABLE_TYPE | VARCHAR(16) | Table types for database-level DML supplemental logging, values can be [HEAP, TAC, LSC], empty means no supplemental logging for DML of all tables. |
| GUARD_STATUS        | VARCHAR(8) | Protect data from being changed<br>\*   ALL: Indicates that all users except SYS cannot change any data in the database<br>\*   NONE: Indicates that data changes are not intercepted. |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

