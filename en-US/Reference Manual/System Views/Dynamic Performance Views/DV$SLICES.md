This view displays the storage-related statistics of the LSC table cold data on all master nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | INTEGER   | Group ID                                      |
| GROUP_NODE_ID    | INTEGER   | Node ID within the group                      |
| OWNER            | VARCHAR(64) | Username of the owner of the slice           |
| TABLESPACE_NAME  | VARCHAR(64) | Name of the tablespace where the slice is located |
| TABLE_NAME       | VARCHAR(64) | Name of the table where the slice is located |
| PARTITION_NAME    | VARCHAR(64) | Name of the partition where the slice is located; NULL for non-partitioned tables |
| SORTED           | BOOLEAN   | Whether it is sorted                          |
| COMPACTED        | BOOLEAN   | Whether it is compacted                       |
| FILE_ID          | BIGINT    | Unique ID of the slice                        |
| FILE_SIZE        | BIGINT    | Size of the slice file, including deleted columns, aligned to 4K |
| ROW_COUNT        | BIGINT    | Number of rows stored in the slice           |