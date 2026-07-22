This view shows all the completed xfmr task information for columnar storage used to convert and generate stable data.

|Field |Type |Description |
|------------------|---------------|------------------------------------------------------------------------------------------------------|
| ID               | BIGINT        | xfmr task number                                                                                             |
| OWNER            | VARCHAR(64)   | Username of the xfmr task owner                                                                              |
| TABLESPACE\_NAME | VARCHAR(64)   | Tablespace name of the xfmr task                                                                             |
| TABLE\_NAME      | VARCHAR(64)   | Table name of the xfmr task                                                                                  |
| PARTITION\_NAME  | VARCHAR(64)   | Partition name of the xfmr task                                                                               |
| TYPE             | VARCHAR(16)   | Type of the xfmr task, including transform, compact, etc.                                                    |
| FORCE            | VARCHAR(16)   | Whether the xfmr task is forced execution                                                                     |
| STATUS           | VARCHAR(16)   | Status of the xfmr task:<br/> * finished: completed execution                                                |
| SLICE\_INFO      | VARCHAR(1024) | Slice information corresponding to the xfmr task:<br/> * Conversion task corresponds to the IDs and row counts of slices to be converted to generated slices<br/> * Merge task corresponds to the IDs and row counts of all slices to be merged to generated slices |
| FILE\_ID         | BIGINT        | File ID corresponding to the generated slice of the xfmr task                                                |
| CREATE\_TIME     | TIMESTAMP(6)     | Creation time of the xfmr task                                                                                |
| START\_TIME      | TIMESTAMP(6)     | Start execution time of the xfmr task                                                                         |
| FINISH\_TIME     | TIMESTAMP(6)     | End execution time of the xfmr task                                                                           |
| EXECUTE\_COUNT   | INTEGER       | Execution count of the xfmr task                                                                              |
| MAX\_MEM\_USE    | BIGINT        | Maximum memory size used during the execution of the xfmr task                                                |
| MAX\_MEM\_QUOTA  | BIGINT        | Maximum memory quota size during the execution of the xfmr task                                              |
| SWAP\_OUT\_SIZE  | BIGINT        | Number of bytes swapped out during the current execution of the xfmr task                                     |
| SWAP\_IN\_SIZE   | BIGINT        | Number of bytes swapped in during the current execution of the xfmr task                                      |
| SWAP\_OUT\_COUNT | INTEGER       | Number of times swapped out during the current execution of the xfmr task                                     |
| SWAP\_IN\_COUNT  | INTEGER       | Number of times swapped in during the current execution of the xfmr task                                      |
| SWAP\_OUT\_TIME  | BIGINT        | Time swapped out during the current execution of the xfmr task                                                 |
| SWAP\_IN\_TIME   | BIGINT        | Time swapped in during the current execution of the xfmr task                                                  |