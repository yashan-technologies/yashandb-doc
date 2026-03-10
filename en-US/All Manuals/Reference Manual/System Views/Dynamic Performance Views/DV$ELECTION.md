This view displays the current node's real-time election status when leader election is enabled in the HA architecture of an ISC distributed cluster. When leader election is disabled, this view has no data.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID              | INTEGER   | Group ID                                        |
| GROUP_NODE_ID         | INTEGER   | Node ID within the group                        |
| LEADER_GROUP_ID       | INTEGER   | Leader's group ID                               |
| LEADER_GROUP_NODE_ID   | INTEGER   | Leader's node ID                                |
| TERM                  | BIGINT    | Current leader's term                           |
| LFN                   | BIGINT    | Log flush sequence number                       |
| LFN_TERM              | BIGINT    | Log flush term                                  |
| STATE                 | VARCHAR(64) | Current node's election state<br/>*   Startup: Starting<br/>*   PreCandidate: Pre-election<br/>*   Candidate: Candidate<br/>*   Follower: Follower<br/>*   Leader: Leader<br/>*   Shutdown: Shutdown<br/>*   Unknown: Unknown |
| LAST_HEARTBEAT_TIME   | TIMESTAMP(6) | The time when the current node last received a heartbeat |
| PEERS                 | VARCHAR(768) | Members of the current node's group             |
| EXTEND_INFO           | JSON      | Supplementary information for all nodes in the group |