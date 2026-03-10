This view displays the histogram information for all partition columns.

|Field |Type |Description |
| --- | --- | --- |
| OWNER            | VARCHAR(64)  | The username to which the table belongs   |
| TABLE_NAME       | VARCHAR(64)  | Table name                                |
| PARTITION_NAME   | VARCHAR(64)  | Partition name                            |
| COLUMN_NAME      | VARCHAR(64)  | Column name                               |
| ENDPOINT_NUMBER   | BIGINT       | Endpoint number                           |
| ENDPOINT_VALUE   | VARCHAR(1000)| Endpoint value                            |
| ENDPOINT_VALUE_RAW| RAW(1000)   | Endpoint binary value                     |
| ENDPOINT_REPEAT_COUNT | BIGINT  | Number of endpoints                       |
| SCOPE            | CHAR(6)      | Reserved field, only applicable to global temporary table <br>* SHARED: Statistics are globally valid <br>* SESSION: Statistics are valid only for the session |