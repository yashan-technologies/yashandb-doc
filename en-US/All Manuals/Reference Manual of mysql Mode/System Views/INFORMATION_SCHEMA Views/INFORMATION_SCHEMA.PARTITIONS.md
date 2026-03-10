This view displays information about foreign key constraints.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_CATALOG                 | VARCHAR(3)  | The catalog name of the Partition table, this value is always def |
| TABLE_SCHEMA                  | VARCHAR(64) | The name of the schema where the Partition table is located |
| TABLE_NAME                    | VARCHAR(64) | The name of the Partition table                |
| PARTITION_NAME                | VARCHAR(64) | The name of the Partition                       |
| SUBPARTITION_NAME             | VARCHAR(64) | The name of the Subpartition                   |
| PARTITION_ORDINAL_POSITION    | BIGINT      | The ordinal position of the Partition          |
| SUBPARTITION_ORDINAL_POSITION | BIGINT      | The ordinal position of the Subpartition       |
| PARTITION_METHOD              | VARCHAR(9)  | The method of the Partition (RANGE, HASH, LIST, REFERENCE) |
| SUBPARTITION_METHOD           | VARCHAR(9)  | The method of the Subpartition (NONE, RANGE, HASH, LIST, REFERENCE) |
| PARTITION_EXPRESSION          | VARCHAR(2048)| The expression of the Partition                |
| SUBPARTITION_EXPRESSION       | VARCHAR(2048)| The expression of the Subpartition             |
| PARTITION_DESCRIPTION          | VARCHAR(4000)| The description of the Partition               |
| TABLE_ROWS                    | BIGINT      | The number of rows in the table                |
| AVG_ROW_LENGTH                | INT         | The average row length                         |
| DATA_LENGTH                   | VARCHAR(1)  | The data length<br>Syntax compatible only, no actual functional meaning.        |
| MAX_DATA_LENGTH               | VARCHAR(1)  | The maximum data length<br>Syntax compatible only, no actual functional meaning.          |
| INDEX_LENGTH                  | VARCHAR(1)  | The index length<br>Syntax compatible only, no actual functional meaning.      |
| DATA_FREE                     | VARCHAR(1)  | The unused space<br>Syntax compatible only, no actual functional meaning.       |
| CREATE_TIME                   | TIMESTAMP   | The creation time                              |
| UPDATE_TIME                   | DATE        | The update time                                |
| CHECK_TIME                    | DATE        | The check time                                 |
| CHECKSUM                      | VARCHAR(1)  | The checksum<br>Syntax compatible only, no actual functional meaning.           |
| PARTITION_COMMENT             | VARCHAR(1)  | Partition comment<br>Syntax compatible only, no actual functional meaning.         |
| NODEGROUP                     | VARCHAR(1)  | Node group<br>Syntax compatible only, no actual functional meaning.           |
| TABLESPACE_NAME               | VARCHAR(64) | The tablespace name                            |