This view displays memory information for stable data import in a distributed lower-latency storage. 

|Field |Type |Description |
| ------------------- | ----------- | -------------------------------- |
| GROUP\_ID           | INTEGER     | Group ID                          |
| GROUP\_NODE\_ID     | INTEGER     | Node ID within the group          |
| HANDLER\_ID         | SMALLINT    | Instance ID                       |
| GLOBAL\_SESSION\_ID | INTEGER     | Global session ID in distributed   |
| TABLE\_NAME         | VARCHAR(64) | Table name                        |
| LOAD\_START         | TIMESTAMP(6)   | Import start time                 |
| LOAD\_TIME          | BIGINT      | Time executed for the current import |
| ROW\_NUM            | BIGINT      | Number of imported records        |
| ROW\_BYTES          | BIGINT      | Number of bytes imported          |
| MEM\_USE            | BIGINT      | Total memory usage for the import |
| MEM\_QUOTA          | BIGINT      | Memory quota for the import      |
| SWAP\_BYTES         | BIGINT      | Memory swap in and out amount    |
| SWAP\_TIME          | BIGINT      | Memory swap in and out time      |
| WRITER\_QUOTA       | BIGINT      | Memory quota used by Cold data writer |
| BUFFER\_QUOTA       | BIGINT      | Memory quota used by import data cache |