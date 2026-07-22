This view displays segment-level statistics.

|Field |Type |Description |
| --- | --- | --- |
| OWNER             | VARCHAR(64)  | Username of the object owner |
| OBJECT_NAME       | VARCHAR(64)  | Object name                 |
| SUBOBJECT_NAME    | VARCHAR(64)  | Partition or subpartition name |
| TABLESPACE_NAME   | VARCHAR(64)  | Tablespace name             |
| TS#               | INTEGER      | Tablespace ID               |
| OBJ#              | BIGINT       | Object ID                   |
| DATAOBJ#          | BIGINT       | Object DATAOBJ ID           |
| OBJECT_TYPE       | VARCHAR(18)  | Object type name            |
| STATISTIC_NAME    | VARCHAR(64)  | Statistics name             |
| STATISTIC#        | TINYINT      | Statistics ID               |
| VALUE             | BIGINT       | Statistics value            |