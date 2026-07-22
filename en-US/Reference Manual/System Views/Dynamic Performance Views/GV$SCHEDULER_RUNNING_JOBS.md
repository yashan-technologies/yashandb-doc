This view displays information about jobs currently running on all instances in the cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER     | Group ID     |
| GROUP_NODE_ID | NUMBER     | Node ID within the group       |
| INST_ID | NUMBER     | Instance ID       |
|  SESSION_ID | SMALLINT   | Session ID           |
| SESSION_SERIAL_NUM | INTEGER    | Session Serial Number             |
| JOB_ID | BIGINT     | Job Identifier                |
| PADDR | BIGINT     | Process ID                |
| OS_PROCESS_ID | VARCHAR(1) | Operating System Process ID (currently NULL)        |
| SESSION_STAT_CPU | VARCHAR(1) | Session CPU Statistics (currently NULL)                 |
| CON_ID | VARCHAR(1) | Container ID (currently NULL)                   |
