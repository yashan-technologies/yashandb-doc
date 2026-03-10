This view displays information about all unfinished xfmr tasks for generating stable data using columnar storage.

|Field |Type |Description |
|------------------|---------------|------------------------------------------------------------------------------------------------------|
| ID               | BIGINT         | xfmr task number                                                                                       |
| OWNER            | VARCHAR(64)    | Username of the owner of the xfmr task                                                                 |
| TABLESPACE_NAME  | VARCHAR(64)    | Name of the tablespace associated with the xfmr task                                                    |
| TABLE_NAME       | VARCHAR(64)    | Name of the table associated with the xfmr task                                                       |
| PARTITION_NAME    | VARCHAR(64)    | Name of the partition associated with the xfmr task                                                   |
| TYPE             | VARCHAR(16)    | Type of the xfmr task, including ac, clean, create, transform, compact, etc.                       |
| FORCE            | VARCHAR(16)    | Whether the xfmr task is a forced execution                                                             |
| STATUS           | VARCHAR(16)    | Status of the xfmr task:<br/> * ready: Created and waiting for execution<br/> * running: Currently executing |
| SLICE_INFO       | VARCHAR(1024)  | Slice information corresponding to the xfmr task:<br/> * The transformation task corresponds to the IDs and rows from the slices that need to be transformed to generate slices<br/> * The merging task corresponds to the IDs and rows from all slices that need to be merged to generate slices |
| CREATE_TIME      | TIMESTAMP      | Creation time of the xfmr task                                                                          |
| START_TIME       | TIMESTAMP      | Start execution time of the xfmr task                                                                   |
| EXECUTE_COUNT    | INTEGER        | Number of executions of the xfmr task                                                                   |
| ERROR_CODE       | INTEGER        | Current failure error code of the xfmr task                                                             |
| ERROR_MESSAGE    | VARCHAR(512)   | Current failure error message of the xfmr task                                                          |
| MEM_USE          | BIGINT         | Current amount of memory used by the xfmr task                                                          |
| MEM_QUOTA        | BIGINT         | Current memory quota size of the xfmr task                                                              |
| SWAP_OUT_SIZE    | BIGINT         | Current size of bytes swapped out during the execution of the xfmr task                                 |
| SWAP_IN_SIZE     | BIGINT         | Current size of bytes swapped in during the execution of the xfmr task                                  |
| SWAP_OUT_COUNT   | INTEGER        | Current number of swaps out during the execution of the xfmr task                                       |
| SWAP_IN_COUNT    | INTEGER        | Current number of swaps in during the execution of the xfmr task                                        |
| SWAP_OUT_TIME    | BIGINT         | Current time of swaps out during the execution of the xfmr task                                         |
| SWAP_IN_TIME     | BIGINT         | Current time of swaps in during the execution of the xfmr task                                          |