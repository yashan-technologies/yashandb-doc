This view displays the partition information of all LOB objects.

|Field |Type |Description |
| --- | --- | --- |
| TABLE\_OWNER         | VARCHAR(64) | The username to which the LOB object belongs |
| TABLE\_NAME          | VARCHAR(64) | The table name to which the LOB object belongs |
| COLUMN\_NAME         | VARCHAR(64) | The column name to which the LOB object belongs |
| LOB\_NAME            | VARCHAR(64) | The name of the LOB object                    |
| PARTITION\_NAME      | VARCHAR(64) | The partition name of the table to which the LOB partition belongs |
| LOB\_PARTITION\_NAME | VARCHAR(64) | The name of the LOB partition                  |
| LOB\_INDPART\_NAME   | VARCHAR(64) | The name of the LOB index partition corresponding to the LOB partition |
| PARTITION\_POSITION   | BIGINT      | The partition number of the LOB partition      |
| COMPOSITE            | VARCHAR(1)  | Whether the LOB partition has a secondary partition  |
| IN\_ROW              | VARCHAR(1)  | Whether the LOB data is stored inline          |
| TABLESPACE\_NAME     | VARCHAR(64) | The name of the tablespace to which the LOB object belongs |
| SEGMENT\_CREATED      | VARCHAR(3)  | Whether the LOB segment is created             |