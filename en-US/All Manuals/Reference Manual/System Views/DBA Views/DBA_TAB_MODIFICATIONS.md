This view shows the table information that has changed since the last statistics collection.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_OWNER      | VARCHAR(64) | The username for the table      |
| TABLE_NAME       | VARCHAR(64) | Table name                      |
| PARTITION_NAME    | VARCHAR(64) | Partition name                  |
| SUBPARTITION_NAME | VARCHAR(1)  | Subpartition name               |
| INSERTS          | BIGINT      | Number of inserted rows         |
| UPDATES          | BIGINT      | Number of updated rows          |
| DELETES          | BIGINT      | Number of deleted rows          |
| TIMESTAMP        | DATE        | Last change time                |
| TRUNCATED        | VARCHAR(1)  | Whether it has been truncated    |
| DROP_SEGMENTS    | BIGINT      | Number of times segments were dropped |