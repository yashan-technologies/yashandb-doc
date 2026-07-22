This view displays the statistics of all standby databases.

|Field |Type |Description |
| --- | --- | --- |
| DEST_ID            | TINYINT    | Standby database ID, corresponding to the ARCHIVE_DEST_x parameter |
| DEST_NAME          | VARCHAR(16) | Parameter name                                                |
| CONNECTION         | VARCHAR(16) | Whether this standby database is connected, connection status <br>\*   CONNECTED: Connected<br>\*   DISCONNECTED: Not connected |
| PEER_ADDR          | VARCHAR(256) | The HA link listener address of the peer database              |
| STATUS             | VARCHAR(16) | The log synchronization status of this standby database <br>\*   NORMAL: Normal<br>\*   PENDING: Primary/standby log synchronization not ready<br>\*   NEED REPAIR: Standby database needs repair <br>\*   UNKNOWN: Not connected, status unknown |
| DATABASE_MODE      | VARCHAR(16) | The operational status of this standby database, i.e., the STATUS field of the V$INSTANCE view of this standby database <br>\*   STARTED: Database process started, no operations allowed on the database in this state<br>\*   MOUNTED: Database process has loaded physical files, minor maintenance operations allowed in this state<br>\*   OPEN: Database is running normally<br>\*   UNKNOWN: Not connected, status unknown |
| RECEIVED_SEQ#      | INTEGER     | The ASN number of the received logs for this standby database     |
| RECEIVED_LFN       | BIGINT      | The sequence number of the received logs for this standby database |
| APPLIED_SEQ#       | INTEGER     | The ASN number of the applied logs for this standby database       |
| APPLIED_LFN        | BIGINT      | The sequence number of the applied logs for this standby database  |
| SYNCHRONIZATION_STATUS | VARCHAR(16) | Reserved field                                              |
| SYNCHRONIZED       | VARCHAR(8)  | Whether the logs of this standby database are synchronized with the primary database, YES/NO |
| GAP_STATUS         | VARCHAR(16) | Indicates whether there is a redo gap in the standby database (redo files are not continuous) <br>\*   NO GAP: No gap<br>\*   HAS GAP: There is a gap |
| DISCONNECT_TIME     | DATE        | The SCN when this standby database got disconnected             |
| DEPOSIT_THREAD#    | TINYINT     | Managed instance number <br>\*   NULL: Indicates the current instance |
| RECEIVED_SCN       | BIGINT      | The SCN of the received logs for this standby database          |
| DB_UNIQUE_NAME     | VARCHAR(31) | The name of the standby database                                 |
| THREAD# | TINYINT | Identifies the instance |
| FLUSH\_LFN | BIGINT | Identifies the log sequence number of the most recent applied redo log |