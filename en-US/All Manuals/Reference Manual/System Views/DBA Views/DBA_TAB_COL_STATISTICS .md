This view displays the column statistics of all tables.

|Field |Type |Description |
| --- |-------------| --- |
| OWNER          | VARCHAR(64)   | Username of the table owner  |
| TABLE_NAME    | VARCHAR(64)   | Table name                   |
| COLUMN_NAME    | VARCHAR(64)   | Column name                  |
| NUM_DISTINCT  | BIGINT        | Number of distinct values     |
| LOW_VALUE     | RAW(1000)     | Binary representation of the minimum value |
| HIGH_VALUE    | RAW(1000)     | Binary representation of the maximum value |
| DENSITY        | FLOAT         | Column density                |
| NUM_NULLS     | BIGINT        | Number of null values         |
| NUM_BUCKETS   | INTEGER       | Number of histogram buckets    |
| AVG_COL_LEN   | INTEGER       | Average column length (in bytes) |
| MAX_COL_LEN   | INTEGER       | Maximum column length (in bytes) |
| LAST_ANALYZED  | DATE          | Time of the last collected statistics |
| SAMPLE_SIZE    | BIGINT        | Number of sampled records      |
| BLOCK_COUNT    | BIGINT        | Number of blocks occupied      |
| GLOBAL_STATS  | VARCHAR(1)    | Whether it is global statistics |
| USER_STATS    | VARCHAR(1)    | Whether it is user-defined statistics |
| HISTOGRAM      | VARCHAR(15)   | Type of histogram              |
| SCOPE          | CHAR(6)      | Scope of temporary table statistics |