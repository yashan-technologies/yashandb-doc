This view displays information related to session resource usage.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                   | NUMBER    | Group ID                                         |
| GROUP_NODE_ID              | NUMBER    | Node ID within the group                         |
| INST_ID                    | NUMBER    | Instance ID                                      |
| SID                        | SMALLINT  | Session ID                                       |
| CURRENT_CONSUMER_GROUP     | VARCHAR(68)| Resource usage group name                         |
| SPA_USE_MEM                | BIGINT    | Current SPA memory size used by the session, in bytes |
| SPA_USE_QUOTA              | BIGINT    | Current quota held by the session, in bytes      |
| SPA_MAX_USE_MEM            | BIGINT    | Maximum SPA memory size used by the session, in bytes |
| STATE                      | VARCHAR(32)| Session state: RUNNING (executing), QUEUED (waiting in queue), IDLE (no command executing), WAITING (waiting for event) |
| QUEUE_ID                   | INTEGER    | Queue sequence number                             |
| PRIORITY                   | INTEGER    | Current weight of the session in the queue       |
| CURRENT_QUEUED_TIME        | BIGINT    | Time waiting in the queue (status is QUEUED), in milliseconds |
| QUEUED_TIME                | BIGINT    | Total waiting time of the session, in milliseconds |
| QUEUE_TIMEOUTS             | INTEGER    | Total number of timeout occurrences while waiting for scheduling |
| PARALLEL_DOWNGRADE_TIMES   | INTEGER    | Number of times parallel resource downgrade occurred within the session |