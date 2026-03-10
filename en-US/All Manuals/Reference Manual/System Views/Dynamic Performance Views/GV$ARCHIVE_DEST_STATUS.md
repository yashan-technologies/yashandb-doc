This view displays the statistics of all standby databases.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | NUMBER    | Group ID                                                                                             |
| GROUP_NODE_ID        | NUMBER    | Node ID within the group                                                                             |
| INST_ID              | NUMBER    | Instance ID                                                                                          |
| DEST_ID              | TINYINT   | Standby database ID, corresponding to the ARCHIVE_DEST_x parameter                                  |
| DEST_NAME            | VARCHAR(16)| Parameter name                                                                                       |
| CONNECTION           | VARCHAR(16)| Whether this standby database is connected, connection status <br>\*   CONNECTED: Connected<br>\*   DISCONNECTED: Not connected |
| PEER_ADDR            | VARCHAR(256)| HA link listener address of the peer database                                                       |
| STATUS               | VARCHAR(16)| Log synchronization status of this standby database  <br>\*   NORMAL: Normal<br>\*   PENDING: Primary/standby log synchronization not ready<br>\*   NEED REPAIR: Standby database needs repair <br>\*   UNKNOWN: Not connected, status unknown |
| DATABASE_MODE        | VARCHAR(16)| Operating status of this standby database, i.e., the STATUS field of the V$INSTANCE view of this standby database <br>\*   STARTED: Database process started; database cannot be operated under this status<br>\*   MOUNTED: Database process has loaded physical files; limited maintenance operations can be performed under this status<br>\*   OPEN: Database is running normally<br>\*   UNKNOWN: Not connected, status unknown |
| RECEIVED_SEQ#       | INTEGER    | ASN number of the log received by this standby database                                              |
| RECEIVED_LFN        | BIGINT     | Sequence number of the log received by this standby database                                          |
| APPLIED_SEQ#        | INTEGER    | ASN number of the logs applied by this standby database                                               |
| APPLIED_LFN         | BIGINT     | Sequence number of the logs applied by this standby database                                          |
| SYNCHRONIZATION_STATUS | VARCHAR(16)| Reserved field                                                                                      |
| SYNCHRONIZED        | VARCHAR(8) | Whether the logs of this standby database are synchronized with the primary database, YES/NO         |
| GAP_STATUS           | VARCHAR(16)| Indicates whether there is a redo GAP in the standby database <br>\*   NO GAP: No GAP<br>\*   HAS GAP: There is a GAP, i.e., redo files are not continuous |
| DISCONNECT_TIME      | DATE       | SCN of disconnection of this standby database                                                        |
| DEPOSIT_THREAD#      | TINYINT    | Managed instance number<br>NULL: indicates current instance                                          |
| RECEIVED_SCN        | BIGINT     | SCN of the logs received by this standby database                                                    |
| DB_UNIQUE_NAME       | VARCHAR(31)| Name of the standby database                                                                          |