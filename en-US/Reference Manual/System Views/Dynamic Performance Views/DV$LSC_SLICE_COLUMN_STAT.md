This view displays the storage-related statistics of the LSC table on all master nodes in the ISC distributed cluster. If partitions exist, it divides and presents the information of each column under each slice according to the partition.

|Field |Type |Description |
|-------------------------|-----------|-----------------------------------|
| GROUP\_ID               | INTEGER   | Group ID                            |
| GROUP\_NODE\_ID         | INTEGER   | Node ID within the group            |
| BO                      | BIGINT    | LSC parent table ID                 |
| OBJ                     | BIGINT    | LSC child table ID (partition ID)   |
| DATAOBJ                 | BIGINT    | LSC child table's data object ID    |
| SLICE\_ID               | BIGINT    | Slice logical ID                     |
| COLUMN\_ID              | SMALLINT  | Column ID                            |
| COLUMN\_FILE\_SIZE      | BIGINT    | Size of the column file in the slice (including data file and meta data file) |
| COLUMN\_DATA\_SIZE      | BIGINT    | Final data size of the column in the slice after encoding and compression (excluding meta data size) |
| COLUMN\_ENCODED\_SIZE   | BIGINT    | Data size of the column in the slice after encoding, before compression (excluding meta data size) |
| COLUMN\_ORIGINAL\_SIZE  | BIGINT    | Size of the original written data for the column in the slice          |