This view is a synonym for DBA_HISTOGRAMS and displays histogram information for all columns.

|Field |Type |Description |
| --- | --- | --- |
| OWNER              | VARCHAR(64) | The owner username of the table                    |
| TABLE_NAME         | VARCHAR(64) | The name of the table                               |
| COLUMN_NAME        | VARCHAR(64) | The name of the column                              |
| ENDPOINT_NUMBER    | BIGINT      | The endpoint number                                 |
| ENDPOINT_VALUE     | VARCHAR(1000) | The endpoint value                                 |
| ENDPOINT_VALUE_RAW | RAW(1000)  | The binary value of the endpoint                    |
| ENDPOINT_REPEAT_COUNT | BIGINT   | The number of endpoints                             |
| SCOPE              | CHAR(6)    | Reserved field, only for global temporary table <br/>* SHARED: statistics are valid globally<br/>* SESSION: statistics are valid only for the session |