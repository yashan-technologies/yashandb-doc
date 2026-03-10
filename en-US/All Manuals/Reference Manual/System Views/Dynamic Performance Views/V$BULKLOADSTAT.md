This view displays memory information for stable data imports using columnar storage.

|Field |Type |Description |
| ------------- | ----------- | -------------------------------- |
| HANDLER_ID   | SMALLINT    | Instance ID                      |
| TABLE_NAME   | VARCHAR(64) | Table name                       |
| LOAD_START   | TIMESTAMP(6)   | Import start time                |
| LOAD_TIME    | BIGINT      | Current import execution time     |
| ROW_NUM      | BIGINT      | Number of imported records        |
| ROW_BYTES    | BIGINT      | Number of bytes imported          |
| MEM_USE      | BIGINT      | Total memory usage for import     |
| MEM_QUOTA    | BIGINT      | Memory quota for the import      |
| SWAP_BYTES   | BIGINT      | Memory swap in and out amount    |
| SWAP_TIME    | BIGINT      | Memory swap in and out time      |
| WRITER_QUOTA | BIGINT      | Memory quota used by the import cold data writer |
| BUFFER_QUOTA | BIGINT      | Memory quota used by the import data cache |