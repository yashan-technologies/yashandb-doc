This view records the reason why SQL child cursors cannot be shared. When an SQL statement cannot share a child cursor for some reason, the system will create a new child cursor. This view is used to diagnose the specific reasons why cursors cannot be shared.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID                  | NUMBER     | Group ID                                                                                                                              |
| GROUP_NODE_ID             | NUMBER     | Node ID within the group                                                                                                              |
| INST_ID                   | NUMBER     | Instance ID                                                                                                                           |
|  SQL_ID                    | VARCHAR(13) | SQL Identifier                                                                                                                        |
| ADDRESS                   | RAW(8)     | Address of the parent cursor                                                                                                          |
| CHILD_ADDRESS             | RAW(8)     | Address of the child cursor                                                                                                           |
| CHILD_NUMBER              | INTEGER    | Child Cursor Number                                                                                                                   |
| OPTIMIZER_MISMATCH        | VARCHAR(1) | Optimizer environment mismatch (Y/N)                                                                                                   |
| STATS_ROW_MISMATCH        | VARCHAR(1) | Statistics row mismatch (Y/N)                                                                                                         |
| BIND_MISMATCH             | VARCHAR(1) | Bind metadata mismatch with existing child cursor (Y/N)                                                                              |
| TRANSLATION_MISMATCH      | VARCHAR(1) | Base object mismatch of existing child cursor (Y/N)                                                                                   |
| BIND_EQUIV_FAILURE        | VARCHAR(1) | Selectivity mismatch of bind values (Y/N)                                                                                             |
| PX_MISMATCH               | VARCHAR(1) | Parameters affecting parallelization mismatch (Y/N)                                                                                  |
| INST_MISMATCH             | VARCHAR(1) | Cluster instance mapping mismatch (Y/N)                                                                                               |
