This view displays the statistics for all partition columns.

|Field |Type |Description |
| --- | --- | --- |
| OWNER        | VARCHAR(64)   | Username of the table owner       |
| TABLE_NAME   | VARCHAR(64)   | Name of the table                 |
| PARTITION_NAME| VARCHAR(64)  | Name of the partition              |
| COLUMN_NAME  | VARCHAR(64)   | Name of the column                |
| NUM_DISTINCT | BIGINT        | Number of unique values            |
| LOW_VALUE    | RAW(1000)     | Binary representation of the minimum value |
| HIGH_VALUE   | RAW(1000)     | Binary representation of the maximum value |
| DENSITY      | FLOAT         | Density of the column              |
| NUM_NULLS    | BIGINT        | Number of null values              |
| NUM_BUCKETS  | BIGINT        | Number of buckets in the histogram |
| AVG_COL_LEN  | INTEGER       | Average column length              |
| MAX_COL_LEN  | INTEGER       | Maximum column length              |
| LAST_ANALYZED| DATE          | Time of the last statistics collection |
| SAMPLE_SIZE  | BIGINT        | Number of records sampled          |
| GLOBAL_STATS | VARCHAR(1)    | Whether it is global statistics    |
| USER_STATS   | VARCHAR(1)    | Whether it is user-defined statistics |
| HISTOGRAM    | VARCHAR(15)   | Type of histogram                  |
| SCOPE        | CHAR(6)      | Scope of the temporary table statistics |