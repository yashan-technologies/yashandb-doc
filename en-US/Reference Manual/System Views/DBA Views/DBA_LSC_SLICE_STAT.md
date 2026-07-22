This view displays information about all LSC table objects.

|Field |Type |Description |
| --- | --- | --- |
| OWNER               | VARCHAR(64)| The username to which the LSC table belongs |
| TABLESPACE_NAME     | VARCHAR(64)| The name of the tablespace where the LSC is located |
| TABLE_NAME          | VARCHAR(64)| The name of the LSC table                |
| SUB_NAME            | VARCHAR(64)| If it is a partition table, indicates the partition table name |
| ACTIVE_SLICES       | BIGINT     | The number of slices in ACTIVE state     |
| FULL_SLICES         | BIGINT     | The number of slices in FULL state       |
| STALE_SLICES        | BIGINT     | The number of slices in STALE state      |
| STABLE_SLICES       | BIGINT     | The number of slices in STABLE state     |
| SORTED_SLICES       | BIGINT     | The number of sorted slices               |
| COMPACTED_SLICES    | BIGINT     | The number of compacted slices            |
| STABLE_ROWGROUP_NUM | NUMBER     | The number of row groups already converted |
| STABLE_ROW_NUM      | NUMBER     | The number of rows converted to slices    |
| STABLE_FILE_SIZE    | NUMBER     | The total size of slices already converted |
| LEAST_SCN           | BIGINT     | The minimum SCN number that can be queried |