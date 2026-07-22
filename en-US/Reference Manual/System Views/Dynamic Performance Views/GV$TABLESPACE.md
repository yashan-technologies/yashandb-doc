This view displays the summary information of all tablespaces.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER    | Group ID                                                                                                             |
| GROUP_NODE_ID    | NUMBER    | Node ID within the group                                                                                             |
| INST_ID          | NUMBER    | Instance ID                                                                                                         |
| ID               | INTEGER   | Tablespace ID number                                                                                                |
| NAME             | VARCHAR(64) | Tablespace name                                                                                                     |
| STATUS           | VARCHAR(16) | Tablespace status<br>*   ONLINE: Online<br>*   OFFLINE: Offline<br>*   READ ONLY: Read-only                         |
| EXTENT_BLOCKS    | INTEGER   | Number of blocks per extent                                                                                          |
| CONTENTS         | VARCHAR(16) | Type of tablespace<br/>* SWAP: Used for intermediate data swap<br/>* UNDO: Used for saving undo data<br/>* TEMPORARY: Used for temporary table data swap<br/>* PERMANENT: Used for saving user data |
| ALLOCATION_TYPE   | VARCHAR(8) | Tablespace extent allocation method<br/>* AUTO: Dynamically extending extent<br/>* UNIFORM: Fixed size extent         |
| MEMORY_MAPPED    | VARCHAR(8) | Whether it is a memory-mapped tablespace (data stays in memory)                                                     |
| ENCRYPTED        | VARCHAR(8) | Whether it is an encrypted tablespace                                                                                 |
| TEMPORARY        | VARCHAR(8) | Whether it is a tablespace with temporary attributes (data will not be persisted)                                    |
| COMPRESSED       | VARCHAR(8) | Whether it is a compressed tablespace                                                                                 |
| SHARED           | VARCHAR(16) | This temporary tablespace type<br>*   SHARED: Shared temporary tablespace<br>*   LOCAL_ON_ALL: Local temporary tablespace |
| ENCRYPT_ALGO     | VARCHAR(64) | If it is an encrypted tablespace, this field indicates the tablespace encryption algorithm. If it is a non-encrypted tablespace, this field is empty |
| PRELOAD\_BEGIN\_TIME | TIMESTAMP(6) | Start time of memory-mapped tablespace preloading |
| PRELOAD\_END\_TIME | TIMESTAMP(6) | End time of memory-mapped tablespace preloading |