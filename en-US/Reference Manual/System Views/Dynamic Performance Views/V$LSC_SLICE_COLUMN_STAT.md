This view displays storage-related statistics for all LSC tables. When partitions exist, it divides and shows the information for each column under each slice according to the partitions.

|Field |Type |Description |
|-------------------------|-----------|-----------------------------------|
| BO                      | BIGINT    | Parent table ID                     |
| OBJ                     | BIGINT    | This table ID (partition ID)       |
| DATAOBJ                 | BIGINT    | This table's data object ID        |
| SLICE_ID                | BIGINT    | Logical ID of the slice            |
| COLUMN_ID               | SMALLINT  | Column ID                           |
| COLUMN_FILE_SIZE        | BIGINT    | File size of the column in the slice (including data file and metadata file) |
| COLUMN_DATA_SIZE        | BIGINT    | Final size of the column data in the slice after encoding and compression (excluding metadata size) |
| COLUMN_ENCODED_SIZE     | BIGINT    | Size of the column data in the slice before compression after encoding (excluding metadata size) |
| COLUMN_ORIGINAL_SIZE    | BIGINT    | Original size of the written data for the column in the slice |