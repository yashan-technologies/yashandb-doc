This view displays information about all subpartition templates.

|Field |Type |Description |
| --- | --- | --- |
| USER_NAME           | VARCHAR(64) | The username of the table     |
| TABLE_NAME          | VARCHAR(64) | The name of the table to which the partition belongs |
| SUBPARTITION_NAME   | VARCHAR(64) | The name of the subpartition   |
| SUBPARTITION_POSITION| BIGINT     | The position of the subpartition within the table |
| TABLESPACE_NAME     | VARCHAR(64) | The name of the tablespace where the subpartition is located |
| HIGH_BOUND          | VARCHAR(4000)| The boundary value string of the subpartition |