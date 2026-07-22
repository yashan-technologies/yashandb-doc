This view shows the statistics of the table queue for the parallel query executed last on the current statement. It can be used to analyze whether the table queue sharding is reasonable and can only be queried during the connection's lifetime.

|Field |Type |Description |
| ---                     | ---         | --- |
| STAGE\_ID              | SMALLINT    | The stage ID |
| TQ\_ID                 | SMALLINT    | Table queue ID |
| SERVER\_TYPE           | VARCHAR(8)  | Table queue type<br>\* PRODUCER<br>\* CONSUMER |
| SLICE\_ID              | SMALLINT    | The shard of the table queue divided by the number of workers |
| WORKER\_ID             | INTEGER     | The ID of the worker responsible for this shard; -1 if it is COORDINATOR |
| NUM\_ROWS              | BIGINT      | The number of rows processed by the table queue shard |
| BYTES                  | BIGINT      | The number of bytes processed by the table queue shard |
| OPEN\_TIME             | INTEGER     | The duration of the table queue shard (in milliseconds) |
| AVG\_LATENCY           | INTEGER     | The average latency time data waits in the queue (in milliseconds) |
| WAITS                  | INTEGER     | The number of waits for CONSUMER to fetch data from the queue shard or for PRODUCER to push data to the queue shard |
| TIMEOUTS               | INTEGER     | The number of timeouts for CONSUMER to fetch data from the queue shard or for PRODUCER to push data to the queue shard | 