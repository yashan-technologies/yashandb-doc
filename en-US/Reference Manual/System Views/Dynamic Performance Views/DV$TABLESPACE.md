This view shows the summary information of all node tablespaces in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | INTEGER    | Group ID                                            |
| GROUP_NODE_ID    | INTEGER    | Node ID within the group                            |
| ID               | INTEGER    | ID number of the tablespace                         |
| NAME             | VARCHAR(64)| Name of the tablespace                              |
| STATUS           | VARCHAR(8) | Status of the tablespace<br>\* ONLINE: Online<br>\* OFFLINE: Offline<br>\* READ ONLY: Read-only |
| EXTENT_BLOCKS    | INTEGER    | Number of blocks contained in each extent          |
| CONTENTS         | VARCHAR(16)| Type of the tablespace<br>\* SWAP: Used for swapping in and out intermediate data<br>\* UNDO: Used to store undo data<br>\* TEMPORARY: Used for swapping in and out temporary table data<br>\* PERMANENT: Used to store user data |
| ALLOCATION_TYPE   | VARCHAR(8) | Tablespace extent allocation method<br>\* AUTO: Dynamically extend extent<br>\* UNIFORM: Fixed size extent |
| MEMORY_MAPPED    | VARCHAR(8) | Whether it is a memory-mapped tablespace (data resides in memory) |
| ENCRYPTED        | VARCHAR(8) | Whether it is an encrypted tablespace               |
| TEMPORARY        | VARCHAR(8) | Whether it has temporary properties (data will not be persisted) |
| COMPRESSED       | VARCHAR(8) | Whether it is a compressed tablespace               |