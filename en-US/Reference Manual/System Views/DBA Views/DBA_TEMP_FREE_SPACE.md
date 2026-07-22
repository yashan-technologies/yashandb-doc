This view describes the usage information of the temporary tablespace in the corresponding instance of the database.

|Field |Type |Description |
| --- | --- | --- |
| TABLESPACE_NAME    | VARCHAR(64) | Tablespace name                       |
| TABLESPACE_SIZE    | NUMBER      | Total size of the tablespace (unit: bytes) |
| ALLOCATED_SPACE     | NUMBER      | Total allocated space (unit: bytes)  |
| FREE_SPACE         | NUMBER      | Currently available space (unit: bytes) |
| SHARED             | VARCHAR(16) | Type of temporary tablespace<br>*   SHARED: Shared temporary tablespace<br>*   LOCAL_ON_ALL: Local temporary tablespace |
| INST_ID            | NUMBER      | Instance ID                           |