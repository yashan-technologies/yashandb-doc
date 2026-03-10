This view displays storage-related statistics of the LSC table cold data on all master nodes.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | NUMBER      | Group ID                                     |
| GROUP_NODE_ID   | NUMBER      | Node ID within the group                     |
| INST_ID         | NUMBER      | Instance ID                                  |
| OWNER           | VARCHAR(64) | Username of the slice owner                  |
| TABLESPACE_NAME | VARCHAR(64) | Name of the tablespace where the slice is located |
| TABLE_NAME      | VARCHAR(64) | Name of the table where the slice is located |
| PARTITION_NAME   | VARCHAR(64) | Name of the partition where the slice is located, NULL if not a partitioned table |
| SORTED          | BOOLEAN     | Whether it is sorted                          |
| COMPACTED       | BOOLEAN     | Whether it is compacted                       |
| FILE_ID         | BIGINT      | Unique ID of the slice                       |
| FILE_SIZE       | BIGINT      | Size of the slice file, including deleted columns, aligned to 4K |
| ROW_COUNT       | BIGINT      | Number of rows stored in the slice           |