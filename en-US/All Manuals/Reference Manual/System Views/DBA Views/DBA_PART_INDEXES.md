This view displays all partition index information.

|Field |Type |Description |
| --- | --- | --- |
| OWNER | VARCHAR(64) | The username of the partition index owner |
| INDEX_NAME | VARCHAR(64) | The name of the index |
| TABLE_NAME | VARCHAR(64) | The name of the table to which the index belongs |
| PARTITIONING_TYPE | VARCHAR(5) | The partition type<br>\*   RANGE<br>\*   HASH<br>\*   LIST |
| SUBPARTITIONING_TYPE | VARCHAR(5) | The subpartition type<br/>\*   RANGE<br/>\*   HASH<br/>\*   LIST |
| PARTITION_COUNT | BIGINT | The number of partitions |
| PARTITIONING_KEY_COUNT | INTEGER | The number of partition keys |
| SUBPARTITIONING_KEY_COUNT | INTEGER | The number of subpartition keys |
| LOCALITY | VARCHAR(6) | The partition index type<br>\* LOCAL<br>\* GLOBAL |
| DEF_TABLESPACE_NAME | VARCHAR(64) | The default tablespace name when adding a partition |
| DEF_PCT_FREE | INTEGER | The default PCTFREE when adding a partition, in percentage |
| DEF_INI_TRANS | INTEGER | The default INI_TRANS when adding a partition, in number (initialization transactions per page) |
| DEF_MAX_TRANS | INTEGER | The default MAX TRANS when adding a partition, in number (maximum transactions allowed per page) |
| DEF_LOGGING | CHAR(1) | The default LOGGING attribute when adding a partition, with values of Y or N |