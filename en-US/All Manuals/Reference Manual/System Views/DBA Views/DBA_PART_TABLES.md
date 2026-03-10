This view displays information about all partition tables.

|Field |Type |Description |
| --- | --- | --- |
| OWNER | VARCHAR(64) | The username of the partition table |
| TABLE_NAME | VARCHAR(64) | Table name |
| PARTITIONING_TYPE | VARCHAR(9) | Partition type<br>\*   RANGE<br>\*   HASH<br>\*   LIST |
| SUBPARTITIONING_TYPE | VARCHAR(9) | Subpartition type<br/>\*   NONE non-composite partition<br/>\*   RANGE<br>\*   HASH<br>\*   LIST |
| PARTITION_COUNT | BIGINT | Number of partitions |
| PARTITIONING_KEY_COUNT | INTEGER | Number of columns in the partition key |
| SUBPARTITIONING_KEY_COUNT | INTEGER | Number of columns in the subpartition key |
| DEF_TABLESPACE_NAME | VARCHAR(64) | Default tablespace name for the partition |
| DEF_PCT_FREE | INTEGER | Default PCTFREE for the partition |
| DEF_INI_TRANS | INTEGER | Default INIT TRANS for the partition |
| DEF_MAX_TRANS | INTEGER | Default MAX TRANS for the partition |
| DEF_LOGGING | CHAR(1) | Default LOGGING attribute for the partition |
| INTERVAL | VARCHAR(1000) | INTERVAL partition key value |