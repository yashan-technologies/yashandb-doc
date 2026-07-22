This view displays information about all unfinished xfmr tasks using column storage for converting to generate stable data.

|Field |Type |Description |
|-------------------|---------------|------------------------------------------------------------------------------------------------------|
| GROUP_ID          | NUMBER        | Group ID                                                                                                      |
| GROUP_NODE_ID     | NUMBER        | Node ID within the group                                                                                       |
| INST_ID           | NUMBER        | Instance ID                                                                                                   |
| ID                | BIGINT        | xfmr task number                                                                                              |
| OWNER             | VARCHAR(64)   | User name of the owner of the xfmr task                                                                       |
| TABLESPACE\_NAME  | VARCHAR(64)   | Name of the tablespace to which the xfmr task belongs                                                         |
| TABLE\_NAME       | VARCHAR(64)   | Name of the table to which the xfmr task belongs                                                              |
| PARTITION\_NAME   | VARCHAR(64)   | Name of the partition to which the xfmr task belongs                                                          |
| TYPE              | VARCHAR(16)   | Type of the xfmr task, including ac, clean, create, transform, compact, etc.                                 |
| FORCE             | VARCHAR(16)   | Whether the xfmr task is executed forcibly                                                                     |
| STATUS            | VARCHAR(16)   | Status of the xfmr task:<br/> * ready: Created and waiting for execution<br/> * running: Currently executing   |
| SLICE\_INFO       | VARCHAR(1024) | Slice information corresponding to the xfmr task:<br/> * The transformation task corresponds to the IDs and row counts of the slices to be transformed and generated<br/> * The merging task corresponds to the IDs and row counts of all slices to be merged and the generated slice  |
| CREATE\_TIME      | TIMESTAMP(6)     | Creation time of the xfmr task                                                                                |
| START\_TIME       | TIMESTAMP(6)     | Start execution time of the xfmr task                                                                         |
| EXECUTE\_COUNT    | INTEGER       | Execution count of the xfmr task                                                                              |
| ERROR\_CODE       | INTEGER       | Current failure error code of the xfmr task                                                                    |
| ERROR\_MESSAGE    | VARCHAR(512)  | Current failure error message of the xfmr task                                                                 |
| MEM\_USE          | BIGINT        | Current memory size used by the xfmr task                                                                     |
| MEM\_QUOTA        | BIGINT        | Current memory quota size for the xfmr task                                                                    |
| SWAP\_OUT\_SIZE   | BIGINT        | Current size of bytes swapped out during execution of the xfmr task                                            |
| SWAP\_IN\_SIZE    | BIGINT        | Current size of bytes swapped in during execution of the xfmr task                                             |
| SWAP\_OUT\_COUNT  | INTEGER       | Current count of swaps out during execution of the xfmr task                                                   |
| SWAP\_IN\_COUNT   | INTEGER       | Current count of swaps in during execution of the xfmr task                                                    |
| SWAP\_OUT\_TIME   | BIGINT        | Current time of swaps out during execution of the xfmr task                                                   |
| SWAP\_IN\_TIME    | BIGINT        | Current time of swaps in during execution of the xfmr task                                                    |