This view displays the current node's real-time election status when leader election is enabled in the HA architecture. When leader election is disabled, this view shows no data.

|Field |Type |Description |
| --- | --- | --- |
| LEADER_GROUP_ID       | INTEGER  | Leader group ID                                                                                        |
| LEADER_GROUP_NODE_ID  | INTEGER  | Node ID of the leader                                                                                 |
| TERM                  | BIGINT   | Current term of the leader                                                                             |
| LFN                   | BIGINT   | Log flush sequence number                                                                              |
| LFN_TERM              | BIGINT   | Term of the log flush                                                                                  |
| STATE                 | VARCHAR(64) | Current election state of the node<br>\*   Startup: Starting<br>\*   PreCandidate: Pre-election<br>\*   Candidate: Candidate<br>\*   Follower: Follower<br>\*   Leader: Leader<br>\*   Shutdown: Shut down<br>\*   Unknown: Unknown |
| LAST_HEARTBEAT_TIME   | TIMESTAMP(6) | The last time the current node received a heartbeat                                                    |
| PEERS                 | VARCHAR(768) | Members of the current node group                                                                      |
| EXTEND_INFO           | JSON     | Additional information of all nodes in the group                                                      |