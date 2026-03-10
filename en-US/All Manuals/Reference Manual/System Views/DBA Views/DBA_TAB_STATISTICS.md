This view displays the statistics of all tables.

|Field |Type |Description |
| --- | --- | --- |
| OWNER               | VARCHAR(64) | Username of the table owner                        |
| TABLE_NAME          | VARCHAR(64) | Table name                                         |
| PARTITION_NAME      | VARCHAR(64) | Partition name                                     |
| PARTITION_POSITION   | BIGINT      | Partition number                                   |
| SUBPARTITION_NAME   | VARCHAR(1)  | Subpartition name                                  |
| SUBPARTITION_POSITION| VARCHAR(1)  | Subpartition number                                |
| OBJECT_TYPE         | VARCHAR(9)  | Object type                                        |
| NUM_ROWS            | BIGINT      | Number of rows                                     |
| BLOCKS              | BIGINT      | Number of blocks                                   |
| EMPTY_BLOCKS        | BIGINT      | Number of empty blocks                             |
| AVG_SPACE           | INTEGER     | Average free space per block (unit: bytes)        |
| CHAIN_CNT           | BIGINT      | Number of row chains                               |
| AVG_ROW_LEN         | INTEGER     | Average row length (unit: bytes)                  |
| SAMPLE_SIZE         | BIGINT      | Number of sampled rows                             |
| LAST_ANALYZED       | DATE        | Last collection date                               |
| GLOBAL_STATS        | VARCHAR(1)  | Whether it is global statistics                    |
| USER_STATS          | VARCHAR(1)  | Whether it is user-defined statistics              |
| LOCKED_STATS        | VARCHAR(1)  | Whether statistics are locked<br/>* Y: Locked<br/>* N: Unlocked<br/>* Empty: Statistics not collected |
| STALE_STATS         | CHAR(1)     | Whether statistics are stale; in the current YAC Deployment mode, the staleness judgment is based solely on the modifications of the current instance<br/>* Y: Stale<br/>* N: Not stale<br/>* Empty: Statistics not collected |
| SCOPE               | CHAR(6)     | Scope of temporary table statistics                |