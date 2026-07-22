This view displays memory information for stable data imports using columnar storage.

|Field |Type |Description |
| ------------- | ----------- | -------------------------------- |
| HANDLER\_ID   | SMALLINT    | Instance ID                      |
| TABLE\_NAME   | VARCHAR(64) | Table name                       |
| LOAD\_START   | TIMESTAMP(6)   | Import start time                |
| LOAD\_TIME    | BIGINT      | Current import execution time     |
| ROW\_NUM      | BIGINT      | Number of imported records        |
| ROW\_BYTES    | BIGINT      | Number of bytes imported          |
| MEM\_USE      | BIGINT      | Total memory usage for import     |
| MEM\_QUOTA    | BIGINT      | Memory quota for the import      |
| SWAP\_BYTES   | BIGINT      | Memory swap in and out amount    |
| SWAP\_TIME    | BIGINT      | Memory swap in and out time      |
| WRITER\_QUOTA | BIGINT      | Memory quota used by the import cold data writer |
| BUFFER\_QUOTA | BIGINT      | Memory quota used by the import data cache |