This view shows all the completed xfmr task information for columnar storage used to convert and generate stable data.

|Field |Type |Description |
|------------------|---------------|------------------------------------------------------------------------------------------------------|
| ID               | BIGINT        | xfmr task number                                                                                             |
| OWNER            | VARCHAR(64)   | Username of the xfmr task owner                                                                              |
| TABLESPACE_NAME | VARCHAR(64)   | Tablespace name of the xfmr task                                                                             |
| TABLE_NAME      | VARCHAR(64)   | Table name of the xfmr task                                                                                  |
| PARTITION_NAME  | VARCHAR(64)   | Partition name of the xfmr task                                                                               |
| TYPE             | VARCHAR(16)   | Type of the xfmr task, including transform, compact, etc.                                                    |
| FORCE            | VARCHAR(16)   | Whether the xfmr task is forced execution                                                                     |
| STATUS           | VARCHAR(16)   | Status of the xfmr task:<br/> * finished: completed execution                                                |
| SLICE_INFO      | VARCHAR(1024) | Slice information corresponding to the xfmr task:<br/> * Conversion task corresponds to the IDs and row counts of slices to be converted to generated slices<br/> * Merge task corresponds to the IDs and row counts of all slices to be merged to generated slices |
| FILE_ID         | BIGINT        | File ID corresponding to the generated slice of the xfmr task                                                |
| CREATE_TIME     | TIMESTAMP     | Creation time of the xfmr task                                                                                |
| START_TIME      | TIMESTAMP     | Start execution time of the xfmr task                                                                         |
| FINISH_TIME     | TIMESTAMP     | End execution time of the xfmr task                                                                           |
| EXECUTE_COUNT   | INTEGER       | Execution count of the xfmr task                                                                              |
| MAX_MEM_USE    | BIGINT        | Maximum memory size used during the execution of the xfmr task                                                |
| MAX_MEM_QUOTA  | BIGINT        | Maximum memory quota size during the execution of the xfmr task                                              |
| SWAP_OUT_SIZE  | BIGINT        | Number of bytes swapped out during the current execution of the xfmr task                                     |
| SWAP_IN_SIZE   | BIGINT        | Number of bytes swapped in during the current execution of the xfmr task                                      |
| SWAP_OUT_COUNT | INTEGER       | Number of times swapped out during the current execution of the xfmr task                                     |
| SWAP_IN_COUNT  | INTEGER       | Number of times swapped in during the current execution of the xfmr task                                      |
| SWAP_OUT_TIME  | BIGINT        | Time swapped out during the current execution of the xfmr task                                                 |
| SWAP_IN_TIME   | BIGINT        | Time swapped in during the current execution of the xfmr task                                                  |