This view displays memory information for stable data import in a distributed lower-latency storage. 

|Field |Type |Description |
| ------------------- | ----------- | -------------------------------- |
| GROUP_ID           | INTEGER     | Group ID                          |
| GROUP_NODE_ID     | INTEGER     | Node ID within the group          |
| HANDLER_ID         | SMALLINT    | Instance ID                       |
| GLOBAL_SESSION_ID | INTEGER     | Global session ID in distributed   |
| TABLE_NAME         | VARCHAR(64) | Table name                        |
| LOAD_START         | TIMESTAMP(6)   | Import start time                 |
| LOAD_TIME          | BIGINT      | Time executed for the current import |
| ROW_NUM            | BIGINT      | Number of imported records        |
| ROW_BYTES          | BIGINT      | Number of bytes imported          |
| MEM_USE            | BIGINT      | Total memory usage for the import |
| MEM_QUOTA          | BIGINT      | Memory quota for the import      |
| SWAP_BYTES         | BIGINT      | Memory swap in and out amount    |
| SWAP_TIME          | BIGINT      | Memory swap in and out time      |
| WRITER_QUOTA       | BIGINT      | Memory quota used by Cold data writer |
| BUFFER_QUOTA       | BIGINT      | Memory quota used by import data cache |