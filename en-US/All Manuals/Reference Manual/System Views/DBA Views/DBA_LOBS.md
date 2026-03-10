This view displays information about all LOB objects.

|Field |Type |Description |
| --- | --- | --- |
| OWNER            | VARCHAR(64) | The username of the owner of the LOB object |
| TABLE_NAME       | VARCHAR(64) | The name of the table to which the LOB object belongs |
| COLUMN_NAME      | VARCHAR(4000)| The name of the column to which the LOB object belongs |
| SEGMENT_NAME     | VARCHAR(64) | The segment name of the LOB object      |
| TABLESPACE_NAME  | VARCHAR(64) | The name of the tablespace to which the LOB object belongs |
| INDEX_NAME       | VARCHAR(64) | The index name of the LOB object       |
| IN_ROW           | VARCHAR(1)  | Whether the LOB data is stored inline  |
| PARTITIONED      | VARCHAR(1)  | Whether it is a partitioned LOB        |
| SEGMENT_CREATED   | VARCHAR(1)  | Whether the LOB object has a segment   |