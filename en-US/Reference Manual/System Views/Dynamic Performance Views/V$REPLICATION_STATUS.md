This view shows the summary information of standby database redo transmission for all nodes in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| THREAD#        | TINYINT   | Instance number                                                                               |
| CONNECTION      | VARCHAR(16) | Primary/standby connection status<br>\*   CONNECTED: Connected<br>\*   DISCONNECTED: Not connected |
| STATUS              | VARCHAR(16) | Log synchronization status<br>\*   NORMAL: Normal<br>\*   NEED REPAIR: Standby database needs repair<br>\*   REDO MISMATCH: Some logs on the standby database do not match those on the primary database. You can execute SQL commands to repair the issue (alter system ignore standby mismatched redo)  |
| PEER_ROLE      | VARCHAR(16) | Remote database role<br>\*   PRIMARY: Primary<br>\*   STANDBY: Standby<br>\*   UNKNOWN: Not connected, role unknown |
| PEER_MODE      | VARCHAR(16) | Remote database operating status, which corresponds to the STATUS field in the remote V$INSTANCE view <br>\*   STARTED: Database process started, database cannot be operated in this state<br>\*   MOUNTED: Database process has loaded physical files, minimal maintenance operations can be performed in this state<br>\*   OPEN: Database is operating normally<br>\*   UNKNOWN: Not connected, status unknown |
| PEER_ADDR      | VARCHAR(256) | HA link listening address of the remote database                                              |
| PEER_POINT     | VARCHAR(16) | Current log flush point of the Primary database, {resetid}-{asn}-{blockid} |
| PEER_LFN       | BIGINT    | Current log sequence number of the Primary database                                          |
| RECEIVED_POINT | VARCHAR(16) | Current log receive point of the Standby database, {resetid}-{asn}-{blockid} |
| RECEIVED_LFN   | BIGINT    | Current log receive sequence number of the Standby database                                   |
| APPLIED_POINT  | VARCHAR(16) | Current log apply point of the Standby database, {resetid}-{asn}-{blockid} |
| APPLIED_LFN    | BIGINT    | Current log apply sequence number of the Standby database                                     |
| TRANSPORT_LAG  | INTEGER   | Log transport lag time (unit: milliseconds)                                                  |
| APPLY_LAG      | INTEGER   | Log apply lag time (unit: milliseconds)                                                      |
| APPLY_FINISH_TIME | INTEGER | Estimated remaining time for log apply (unit: milliseconds)                                  |
| GAP_SEQ#       | INTEGER   | Minimum log sequence number of redo GAP, 0 indicates no redo GAP                             |
| ERROR          | VARCHAR(512) | This field indicates the error reason for NEED REPAIR of the standby database                |
| TIME_SINCE_LAST_MSG | INTEGER | Time elapsed (in seconds) since the last message was received from the standby database      |
| PEER_NODE_ID   | VARCHAR(16) | NODEID of the remote database                                                                  |
| TRIGGER_COND_FAILOVER | VARCHAR(16) | Whether the Primary database has triggered a conditional failover                           |



> **Note**: 
>
> {resetid}\_{asn}\_{blockid}
>
> - resetid: The reset id of the redo log, this value increments by 1 each time the redo timeline is reset.
> - asn: The archive sequence number, asn increments by 1 each time a redo is generated, each redo has a different asn.
> - blockid: The ID of the page within the redo file, the offset of the page is block id \* block size.

