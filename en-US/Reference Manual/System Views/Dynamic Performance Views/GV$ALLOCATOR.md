This view shows the current memory usage status.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| NAME | VARCHAR(64) | Memory type |
| TOTAL_MEMORY | BIGINT | Total allocated memory size (unit: bytes) |
| CURR_MEMORY_USED | BIGINT | Current total memory used (unit: bytes) |
| FREE_MEMORY | BIGINT | Current remaining memory size (unit: bytes) |
| MAX_MEMORY_USED | BIGINT | Peak memory used during the instance's current run (unit: bytes) |