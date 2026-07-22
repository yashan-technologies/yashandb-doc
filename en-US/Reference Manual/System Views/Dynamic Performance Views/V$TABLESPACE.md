This view displays summary information for all tablespaces.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | Tablespace ID number |
| NAME | VARCHAR(64) | Tablespace name |
| STATUS | VARCHAR(16) | Tablespace status<br>\*   ONLINE: Online<br>\*   OFFLINE: Offline<br>\*   READ ONLY: Read-only |
| EXTENT_BLOCKS | INTEGER | Number of blocks contained in each extent |
| CONTENTS | VARCHAR(16) | Type of tablespace<br/>* SWAP: Used for swapping in and out intermediate data<br/>* UNDO: Used for storing undo data<br/>* TEMPORARY: Used for swapping in and out temporary table data<br/>* PERMANENT: Used for storing user data |
| ALLOCATION_TYPE | VARCHAR(8) | Tablespace extent allocation method<br/>* AUTO: Dynamically extending extents<br/>* UNIFORM: Fixed size extents |
| MEMORY_MAPPED | VARCHAR(8) | Whether it is a memory-mapped tablespace (data resides in memory) |
| ENCRYPTED | VARCHAR(8) | Whether it is an encrypted tablespace |
| TEMPORARY | VARCHAR(8) | Whether it is a tablespace with temporary attributes (data will not be persisted) |
| COMPRESSED | VARCHAR(8) | Whether it is a compressed tablespace |
| SHARED | VARCHAR(16) | Type of temporary tablespace<br>\*   SHARED: Shared temporary tablespace<br>\*   LOCAL_ON_ALL: Local temporary tablespace |
| ENCRYPT_ALGO | VARCHAR(64) | If it is an encrypted tablespace, this indicates the encryption algorithm; if it is a non-encrypted tablespace, this field is empty |
| PRELOAD\_BEGIN\_TIME | TIMESTAMP(6) | Start time of memory-mapped tablespace preloading |
| PRELOAD\_END\_TIME | TIMESTAMP(6) | End time of memory-mapped tablespace preloading |