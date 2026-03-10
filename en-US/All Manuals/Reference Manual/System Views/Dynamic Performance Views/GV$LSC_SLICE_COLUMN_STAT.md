This view displays the storage-related statistics of LSC tables on all master nodes. When partitions exist, it divides and presents information for each column under each slice according to the partition.

|Field |Type |Description |
|------------------------|----------|----------------------------------|
| GROUP_ID               | NUMBER   | Group ID                           |
| GROUP_NODE_ID          | NUMBER   | Node ID within the group           |
| INST_ID                | NUMBER   | Instance ID                        |
| BO                     | BIGINT   | Parent table ID                    |
| OBJ                    | BIGINT   | This table ID (partition ID)      |
| DATAOBJ                | BIGINT   | Data object ID of this table       |
| SLICE_ID               | BIGINT   | Slice logical ID                   |
| COLUMN_ID              | SMALLINT | Column ID                          |
| COLUMN_FILE_SIZE       | BIGINT   | File size of this column in the slice (including data file and metadata file) |
| COLUMN_DATA_SIZE       | BIGINT   | Final data size of this column in the slice after encoding and compression (excluding metadata size) |
| COLUMN_ENCODED_SIZE    | BIGINT   | Data size of this column in the slice after encoding and before compression (excluding metadata size) |
| COLUMN_ORIGINAL_SIZE    | BIGINT   | Original written data size of this column in the slice           |