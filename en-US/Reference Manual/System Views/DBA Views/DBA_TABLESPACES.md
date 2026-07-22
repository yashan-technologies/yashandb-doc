This view displays information about all tablespaces.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | Tablespace ID |
| TABLESPACE\_NAME | VARCHAR(64) | Tablespace Name |
| BLOCK\_SIZE | INTEGER | Size of a single block (unit: bytes) |
| MAX\_SIZE | NUMBER | Maximum capacity that this tablespace can reach (unit: bytes) |
| TOTAL\_BYTES | NUMBER | Actual capacity of this tablespace (unit: bytes) |
| USER\_BYTES | NUMBER | Currently available free space in this tablespace (unit: bytes) |
| USER\_BLOCKS | NUMBER | Current number of available free blocks in this tablespace |
| STATUS | VARCHAR(16) | Tablespace status<br>\*   ONLINE: Online<br>\*   OFFLINE: Offline<br>*   READ ONLY: Read-only |
| CONTENTS | VARCHAR(16) | Storage type of the tablespace<br>\*   SWAP: SWAP tablespace<br>\*   UNDO: UNDO tablespace<br>\*   TEMPORARY: Temporary tablespace<br>\*   PERMANENT: Permanent tablespace |
| LOGGING | VARCHAR(9) | Whether the tablespace logs<br>\*   LOGGING: Logging<br>\*   NOLOGGING: No logging |
| ALLOCATION\_TYPE | VARCHAR(8) | Tablespace extent allocation method<br>\*   AUTO: Dynamic extent expansion<br>\*   UNIFORM: Fixed-length extent |
| SEGMENT\_SPACE\_MANAGEMENT | CHAR(6) | Management method of the tablespace<br>\*   BITMAP |
| ENCRYPTED | VARCHAR(1) | Whether the tablespace is encrypted<br>\*   Y<br>\*   N |
| COMPRESSED | VARCHAR(1) | Whether the tablespace is compressed<br>\*   Y: Compressed<br>\*   N: Not compressed |
| SHARED | VARCHAR(16) | Type of the temporary tablespace<br>\*   SHARED: Shared temporary tablespace<br>\*   LOCAL_ON_ALL: Local temporary tablespace |
| BIGFILE | CHAR(2) | Compatible field, has no practical significance |