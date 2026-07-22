This view describes the relevant statistics of YAC background threads.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| TASK_ID | INTEGER | Task number |
| SESSION_ID | INTEGER | Session number |
| TASK_TYPE | VARCHAR(32) | Task type<br>\*   AXC_GRC_TASK: GRC thread<br>\*   AXC_GCS_TASK: GCS thread<br>\*   AXC_GLS_TASK: GLS thread<br>\*   AXC_REFORM_TASK: reform thread<br>\*   AXC_BATCH_TASK: batch processing thread<br>\*   AXC_BCST_TASK: DDL type thread<br>\*   AXC_AUX_TASK: auxiliary thread<br>\*   AXC_SMON_TASK: deadlock detection thread<br>\*   AXC_RCYTEMP_TASK: temporary tablespace recovery thread |
| CELL_ID | INTEGER | Message queue number corresponding to the task |
| PROCESS_MESSAGE_NUM | BIGINT | Number of messages processed |
| CORE_TASK | BOOLEAN | Is it a core thread |