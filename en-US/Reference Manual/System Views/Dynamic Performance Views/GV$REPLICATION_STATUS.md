This view displays the summary information of standby database redo transmission for all nodes in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | NUMBER    | Group ID                                                 |
| GROUP_NODE_ID       | NUMBER    | Node ID within the group                                  |
| INST_ID             | NUMBER    | Instance ID                                              |
| THREAD#             | TINYINT   | Instance Number                                          |
| CONNECTION          | VARCHAR(16) | Primary/standby connection status<br>\*   CONNECTED: Connected<br>\*   DISCONNECTED: Not connected |
| STATUS              | VARCHAR(16) | Log synchronization status<br>\*   NORMAL: Normal<br>\*   NEED REPAIR: Standby database needs repair<br>\*   REDO MISMATCH: Some logs on the standby database do not match those on the primary database. You can execute SQL commands to repair the issue (alter system ignore standby mismatched redo)  |
| PEER_ROLE           | VARCHAR(16) | Peer database role<br>\*   PRIMARY: Primary<br>\*   STANDBY: Standby<br>\*   UNKNOWN: Not connected, role unknown |
| PEER_MODE           | VARCHAR(16) | Peer database running status, which corresponds to the STATUS field in the peer V$INSTANCE view<br>\*   STARTED: Database process started, cannot operate the database in this state<br>\*   MOUNTED: Database process has loaded physical files, some maintenance operations are allowed in this state<br>\*   OPEN: Database is operating normally<br>\*   UNKNOWN: Not connected, status unknown |
| PEER_ADDR           | VARCHAR(256) | HA link listening address of the peer database           |
| PEER_POINT          | VARCHAR(16) | Current log flushing point of the Primary database,{resetid}-{asn}-{blockid} |
| PEER_LFN            | BIGINT    | Current log sequence number of the Primary database     |
| RECEIVED_POINT      | VARCHAR(16) | Current log receiving point of the Standby database,{resetid}-{asn}-{blockid} |
| RECEIVED_LFN        | BIGINT    | Current log receiving sequence number of the Standby database |
| APPLIED_POINT       | VARCHAR(16) | Current log apply point of the Standby database,{resetid}-{asn}-{blockid} |
| APPLIED_LFN         | BIGINT    | Current log apply sequence number of the Standby database |
| TRANSPORT_LAG       | INTEGER    | Log transmission delay time (unit: milliseconds)        |
| APPLY_LAG           | INTEGER    | Log apply delay time (unit: milliseconds)               |
| APPLY_FINISH_TIME   | INTEGER    | Estimated remaining time for log apply (unit: milliseconds) |
| GAP_SEQ#            | INTEGER    | Minimum log sequence number of redo GAP; 0 indicates no redo GAP |
| ERROR               | VARCHAR(512) | This field indicates the reason for NEED REPAIR in the standby database |
| TIME_SINCE_LAST_MSG | INTEGER    | Time elapsed since the last message was received from the standby database (unit: seconds) |
| PEER_NODE_ID        | VARCHAR(16) | NODEID of the peer database                               |
| TRIGGER_COND_FAILOVER | VARCHAR(16) | Whether a conditional failover has been triggered by the Primary database | 



> **Note**: 
>
> {resetid}\_{asn}\_{blockid}
>
> - resetid: The reset id of the redo log, this value increments by 1 each time the redo timeline is reset.
> - asn: The archive sequence number, asn increments by 1 each time a redo is generated, each redo has a different asn.
> - blockid: The ID of the page within the redo file, the offset of the page is block id \* block size.

