This view displays the storage-related statistics of cold data for all LSC tables, presented by slice.

|Field |Type |Description |
| --- | --- | --- |
| OWNER              | VARCHAR(64)  | Username of the slice owner                         |
| TABLESPACE_NAME    | VARCHAR(64)  | Name of the tablespace where the slice is located   |
| TABLE_NAME         | VARCHAR(64)  | Name of the table where the slice is located        |
| PARTITION_NAME     | VARCHAR(64)  | Name of the partition where the slice is located; NULL for non-partitioned tables |
| SORTED             | BOOLEAN      | Whether it is sorted                                |
| COMPACTED          | BOOLEAN      | Whether it is compacted                             |
| FILE_ID            | BIGINT       | Unique ID of the slice                              |
| FILE_SIZE          | BIGINT       | Size of the slice file, including deleted columns, 4K aligned |
| ROW_COUNT          | BIGINT       | Number of rows stored in the slice                 |