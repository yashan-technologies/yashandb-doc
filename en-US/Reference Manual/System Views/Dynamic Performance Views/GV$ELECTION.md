This view shows the real-time election status of the current node when leader election is enabled in the HA architecture. No data is available in this view when leader election is disabled.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID           | NUMBER   | Group ID                                 |
| GROUP_NODE_ID      | NUMBER   | Node ID within the group                 |
| INST_ID            | NUMBER   | Instance ID                              |
| LEADER_GROUP_ID    | INTEGER  | Leader group ID                          |
| LEADER_GROUP_NODE_ID | INTEGER | Leader node's ID                        |
| TERM               | BIGINT   | Current leader's term                   |
| LFN                | BIGINT   | Log flush sequence number                |
| LFN_TERM           | BIGINT   | Term of the log flush                   |
| STATE              | VARCHAR(64) | Current node election state<br>\* Startup: Startup<br>\* PreCandidate: Pre-election<br>\* Candidate: Candidate<br>\* Follower: Follower<br>\* Leader: Leader<br>\* Shutdown: Shutdown<br>\* Unknown: Unknown |
| LAST_HEARTBEAT_TIME | TIMESTAMP(6) | Last time the current node received a heartbeat |
| PEERS              | VARCHAR(768) | Members of the current node's group     |
| EXTEND_INFO        | JSON     | Supplementary information of all nodes in the group |