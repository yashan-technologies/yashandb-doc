This view displays the partition information of all LOB objects.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_OWNER         | VARCHAR(64) | The username to which the LOB object belongs |
| TABLE_NAME          | VARCHAR(64) | The table name to which the LOB object belongs |
| COLUMN_NAME         | VARCHAR(64) | The column name to which the LOB object belongs |
| LOB_NAME            | VARCHAR(64) | The name of the LOB object                    |
| PARTITION_NAME      | VARCHAR(64) | The partition name of the table to which the LOB partition belongs |
| LOB_PARTITION_NAME | VARCHAR(64) | The name of the LOB partition                  |
| LOB_INDPART_NAME   | VARCHAR(64) | The name of the LOB index partition corresponding to the LOB partition |
| PARTITION_POSITION   | BIGINT      | The partition number of the LOB partition      |
| COMPOSITE            | VARCHAR(1)  | Whether the LOB partition has a secondary partition  |
| IN_ROW              | VARCHAR(1)  | Whether the LOB data is stored inline          |
| TABLESPACE_NAME     | VARCHAR(64) | The name of the tablespace to which the LOB object belongs |
| SEGMENT_CREATED      | VARCHAR(3)  | Whether the LOB segment is created             |