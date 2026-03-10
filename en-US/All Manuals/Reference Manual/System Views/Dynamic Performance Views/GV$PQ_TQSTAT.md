This view displays the statistics of the table queue for parallel queries that were last executed on the current statement. It is useful for analyzing whether the table queue partitioning is reasonable and can only be queried during the lifetime of the connection.

|Field |Type |Description |
| ---                     |-----------| --- |
| GROUP_ID                | NUMBER      | Group ID     |
| GROUP_NODE_ID           | NUMBER      | Node ID within the group |
| INST_ID                 | NUMBER      | Instance ID  |
| STAGE_ID                | SMALLINT    | Current stage ID |
| TQ_ID                   | SMALLINT    | Table queue ID |
| SERVER_TYPE             | VARCHAR(8)  | Table queue type<br>\* PRODUCER<br>\* CONSUMER |
| SLICE_ID                | SMALLINT    | Partition of the table queue based on the number of workers |
| WORKER_ID               | INTEGER      | ID of the worker responsible for the partition; -1 if it is a COORDINATOR |
| NUM_ROWS                | BIGINT      | Number of rows processed by the table queue partition |
| BYTES                   | BIGINT      | Number of bytes processed by the table queue partition |
| OPEN_TIME               | INTEGER      | Duration of the table queue partition (in milliseconds) |
| AVG_LATENCY             | INTEGER      | Average latency time (in milliseconds) that data waits in the queue |
| WAITS                   | INTEGER      | Number of waits for CONSUMER to fetch data from the queue partition or for PRODUCER to push data to the queue partition |
| TIMEOUTS                | INTEGER      | Number of timeouts for CONSUMER to fetch data from the queue partition or for PRODUCER to push data to the queue partition |