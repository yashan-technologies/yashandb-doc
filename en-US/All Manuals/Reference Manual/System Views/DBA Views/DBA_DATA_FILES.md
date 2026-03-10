This view displays information about all data files.

|Field |Type |Description |
| --- | --- | --- |
| FILE_NAME      | VARCHAR(255)| File name                                       |
| FILE_ID        | INTEGER     | File ID                                         |
| TABLESPACE_NAME| VARCHAR(64) | Name of the tablespace to which the file belongs|
| BYTES            | BIGINT      | File size (unit: bytes)                         |
| BLOCKS           | INTEGER     | Number of blocks contained in the file          |
| STATUS           | VARCHAR(8)  | File status<br>\*   ONLINE: Online<br>\*   OFFLINE: Offline<br>\* RECOVER: Pending recovery |
| MAXBYTES         | BIGINT      | Maximum file size (unit: bytes)                 |
| MAXBLOCKS        | NUMBER      | Maximum number of blocks contained in the file  |
| AUTO_EXTEND    | VARCHAR(8)  | Whether the file can auto-extend<br>\*   ON: Can auto-extend<br>\*   OFF: Cannot auto-extend |
| NEXT_SIZE      | BIGINT      | Size of each automatic extension (unit: bytes)  |
| USER_BYTES     | BIGINT      | Available user data space in a file. Equals actual file size minus metadata (unit: bytes) |
| USER_BLOCKS    | INTEGER     | Blocks available for user data storage in the file |
| ONLINE_STATUS   | VARCHAR(8)  | File status<br>\*   ONLINE: Online<br>\*   OFFLINE: Offline |
| AUTOEXTENSIBLE   | VARCHAR(3)  | Whether the file can auto-extend. Consistent with the AUTO_EXTEND field, used for syntax compatibility<br>\*   YES: Can auto-extend<br>\*   NO: Cannot auto-extend |