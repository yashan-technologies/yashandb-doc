This view displays the secondary partition information for all LOB objects.

|Field |Type |Description |
| ---------------------- | ----------- | -------------------------------- |
| TABLE\_OWNER           | VARCHAR(64) | The username to which the LOB object belongs |
| TABLE\_NAME            | VARCHAR(64) | The table name to which the LOB object belongs |
| COLUMN\_NAME           | VARCHAR(64) | The column name to which the LOB object belongs |
| LOB\_NAME              | VARCHAR(64) | The name of the LOB object        |
| LOB\_PARTITION\_NAME   | VARCHAR(64) | The name of the LOB partition     |
| SUBPARTITION_NAME      | VARCHAR(64) | The name of the secondary partition to which the LOB secondary partition belongs |
| LOB_SUBPARTITION_NAME  | VARCHAR(64) | The name of the LOB secondary partition |
| LOB\_INDSUBPART\_NAME  | VARCHAR(64) | The name of the LOB index secondary partition corresponding to the LOB secondary partition |
| SUBPARTITION\_POSITION | BIGINT      | The number of the LOB secondary partition |
| IN\_ROW                | VARCHAR(1)  | Whether the LOB data is stored inline |
| TABLESPACE\_NAME       | VARCHAR(64) | The name of the tablespace to which the LOB object belongs |
| SEGMENT\_CREATED       | VARCHAR(1)  | Whether the LOB segment is created |