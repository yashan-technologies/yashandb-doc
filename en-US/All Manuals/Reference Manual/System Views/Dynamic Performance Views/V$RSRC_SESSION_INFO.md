This view displays resource usage information related to sessions.

|Field |Type |Description |
| --- | --- | --- |
| SID | SMALLINT | Session ID |
| CURRENT_CONSUMER_GROUP | VARCHAR(68) | Resource usage group name |
| SPA_USE_MEM | BIGINT | Current SPA memory size used by the session, in bytes |
| SPA_USE_QUOTA | BIGINT | Current quota occupied by the session, in bytes |
| SPA_MAX_USE_MEM | BIGINT | Maximum SPA memory size used by the session, in bytes |
| STATE | VARCHAR(32) | Session state: RUNNING executing, QUEUED queued, IDLE currently no commands executing, WAITING waiting for events |
| QUEUE_ID | INTEGER | Queue sequence number |
| PRIORITY | INTEGER | Current weight of the session in the queue |
| CURRENT_QUEUED_TIME | BIGINT | Waiting time in the queue (state is QUEUED), in milliseconds |
| QUEUED_TIME | BIGINT | Total accumulated waiting time of the session (milliseconds) |
| QUEUE_TIMEOUTS | INTEGER | Total accumulated timeout occurrences waiting for scheduling |
| PARALLEL_DOWNGRADE_TIMES | INTEGER | Number of times parallel resource downgrading occurs within the session |