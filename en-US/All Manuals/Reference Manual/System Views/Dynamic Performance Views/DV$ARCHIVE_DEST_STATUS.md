This view displays the statistics of all standby databases in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | INTEGER    | Group ID                                             |
| GROUP_NODE_ID     | INTEGER    | Node ID within the group                             |
| DEST_ID           | TINYINT    | Standby database ID, corresponds to ARCHIVE_DEST_x parameter |
| CONNECTION         | VARCHAR(16) | Whether this standby database is connected, connection status <br>\*   CONNECTED: Connected<br>\*   DISCONNECTED: Not connected |
| PEER_ADDR         | VARCHAR(256) | HA link listening address of the peer database      |
| STATUS            | VARCHAR(16) | Log synchronization status of this standby database  <br>\*   NORMAL: Normal<br>\*   PENDING: Primary/standby log synchronization not ready<br>\*   NEED REPAIR: Standby database needs repair<br>\*   REDO MISMATCH: Some logs of the standby database do not match the primary database; SQL commands can be executed to repair (alter system ignore standby mismatched redo)<br>\*   UNKNOWN: Not connected, status unknown |
| DATABASE_MODE     | VARCHAR(16) | Operating status of this standby database, i.e., the STATUS field of the V$INSTANCE view of this standby database<br>\*   STARTED: Database process started, operations on the database are not possible in this state<br>\*   MOUNTED: Database process has loaded physical files, minor maintenance operations are possible in this state<br>\*   OPEN: Database is operating normally<br>\*   UNKNOWN: Not connected, status unknown |
| RECEIVED_SEQ#     | INTEGER    | ASN number of the received log for this standby database |
| RECEIVED_LFN      | BIGINT     | Sequence number of the received log for this standby database |
| APPLIED_SEQ#      | INTEGER    | ASN number of the applied log for this standby database |
| APPLIED_LFN       | BIGINT     | Sequence number of the applied log for this standby database |
| SYNCHRONIZATION_STATUS | VARCHAR(16) | Reserved field                                   |
| SYNCHRONIZED      | VARCHAR(8)  | Whether the logs of this standby database have been synchronized with the primary database, YES/NO |
| GAP_STATUS        | VARCHAR(16) | Indicates whether there is a redo gap in the standby database (redo files are not continuous) <br><br>\*   NO GAP: No gap<br>\*   HAS GAP: There is a gap |
| DISCONNECT_TIME   | DATE       | The SCN at which this standby database was disconnected |
| DEPOSIT_THREAD#   | TINYINT    | Managed instance number<br>\*  NULL: Indicates the current instance |
| RECEIVED_SCN      | BIGINT     | SCN of the received log for this standby database    |