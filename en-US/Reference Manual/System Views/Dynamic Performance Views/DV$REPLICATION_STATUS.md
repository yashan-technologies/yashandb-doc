This view shows the summary information of standby database redo transmission for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | INTEGER   | Group ID                                                                                                             |
| GROUP_NODE_ID        | INTEGER   | Node ID within the group                                                                                             |
| THREAD#              | TINYINT   | Instance number                                                                                                     |
| CONNECTION           | VARCHAR(16) | Primary/standby connection status<br>\*   CONNECTED: Connected<br>\*   DISCONNECTED: Not connected                 |
| STATUS               | VARCHAR(16) | Log synchronization status<br>\*   NORMAL: Normal<br>\*   NEED REPAIR: Standby database needs repair                 |
| PEER_ROLE            | VARCHAR(16) | Peer database role<br>\*   PRIMARY: Primary<br>\*   STANDBY: Standby<br>\*   UNKNOWN: Not connected, role unknown  |
| PEER_MODE            | VARCHAR(16) | Peer database operating status, i.e., the STATUS field of the peer V$INSTANCE view<br>\*   STARTED: Database process started, cannot operate the database in this state<br>\*   MOUNTED: Database process has loaded physical files, limited maintenance operations can be performed in this state<br>\*   OPEN: Database is running normally<br>\*   UNKNOWN: Not connected, status unknown |
| PEER_ADDR            | VARCHAR(256) | HA link listen address of the peer database                                                                           |
| PEER_POINT           | VARCHAR(16) | Current log flush point of the Primary database, {resetid}-{asn}-{blockid}                 |
| PEER_LFN            | BIGINT    | Current log sequence number of the Primary database                                                                  |
| RECEIVED_POINT       | VARCHAR(16) | Current log receipt point of the Standby database, {resetid}-{asn}-{blockid}               |
| RECEIVED_LFN         | BIGINT    | Current log receipt sequence number of the Standby database                                                          |
| APPLIED_POINT        | VARCHAR(16) | Current log apply point of the Standby database, {resetid}-{asn}-{blockid}                 |
| APPLIED_LFN          | BIGINT    | Current log apply sequence number of the Standby database                                                            |
| TRANSPORT_LAG        | INTEGER   | Log transmission delay time (unit: milliseconds)                                                                    |
| APPLY_LAG            | INTEGER   | Log apply delay time (unit: milliseconds)                                                                           |
| APPLY_FINISH_TIME    | INTEGER   | Estimated remaining time for log apply (unit: milliseconds)                                                        |
| GAP_SEQ#             | INTEGER   | Minimum log sequence number of the redo GAP, 0 indicates no redo GAP available                                        |
| ERROR                | VARCHAR(512) | This field indicates the reason for the standby database NEED REPAIR error                                           |
| TIME_SINCE_LAST_MSG  | INTEGER   | Time elapsed since the last message received from the standby database (unit: seconds)                             |
| PEER_NODE_ID         | VARCHAR(16) | NODEID of the peer database                                                                                          |
| TRIGGER_COND_FAILOVER | VARCHAR(16) | Whether the Primary database has triggered a conditional failover                                                   |



> **Note**: 
>
> {resetid}_{asn}_{blockid}
>
> - resetid: The reset id of the redo log, this value increments by 1 each time the redo timeline is reset.
> - asn: The archive sequence number, asn increments by 1 each time a redo is generated, each redo has a different asn.
> - blockid: The ID of the page within the redo file, the offset of the page is block id \* block size.

