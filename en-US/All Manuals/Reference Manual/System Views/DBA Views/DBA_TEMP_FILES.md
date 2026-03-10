This view describes all temporary file information in the database.

|Field |Type |Description |
| --- |--------------| --- |
| FILE_ID        | INTEGER        | Temporary File ID |
| FILE_NAME      | VARCHAR(255)   | Temporary File Name |
| STATUS          | VARCHAR(8)     | File Status<br>\*   OFFLINE: Offline<br>\*   ONLINE: Online |
| BYTES           | BIGINT         | Temporary File Size (Unit: Bytes) |
| BLOCKS          | INTEGER        | Number of blocks contained in the temporary file |
| RELATIVE_FNO   | INTEGER        | Internal number within the tablespace |
| AUTOEXTENSIBLE  | VARCHAR(8)     | Whether the temporary file can automatically extend<br>\*   ON: Can automatically extend<br>\*   OFF: Cannot automatically extend |
| TABLESPACE_NAME| VARCHAR(64)    | Name of the tablespace to which the temporary file belongs |
| MAXBYTES        | BIGINT         | Maximum file size (Unit: Bytes) |
| MAXBLOCKS       | NUMBER         | Maximum number of blocks the file can contain |
| INCREMENT_BY   | NUMBER         | Default increment for automatic extension (Unit is 1 block) |
| USER_BYTES     | BIGINT         | Currently available free data size in the temporary file (Unit: Bytes) |
| USER_BLOCKS    | INTEGER        | Current number of free available blocks in the temporary file |
| SHARED          | VARCHAR(16)    | Type of temporary tablespace corresponding to the temporary file<br>\*   SHARED: Shared temporary tablespace<br>\*   LOCAL_ON_ALL: Local temporary tablespace |
| INST_ID        | VARCHAR(44)    | Instance ID corresponding to the temporary file in the local temporary tablespace |