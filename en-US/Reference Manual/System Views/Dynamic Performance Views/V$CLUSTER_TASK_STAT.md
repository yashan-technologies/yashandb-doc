This view describes the relevant statistics of YAC background threads.

|Field |Type |Description |
| --- | --- | --- |
| TASK_ID | INTEGER | Task ID |
| SESSION_ID | INTEGER | Session ID |
| TASK_TYPE | VARCHAR(32) | Task type<br>\*   AXC_GRC_TASK: GRC thread<br>\*   AXC_GCS_TASK: GCS thread<br>\*   AXC_GLS_TASK: GLS thread<br>\*   AXC_REFORM_TASK: Reform thread<br>\*   AXC_BATCH_TASK: Batch processing thread<br>\*   AXC_BCST_TASK: DDL type thread<br>\*   AXC_AUX_TASK: Auxiliary thread<br>\*   AXC_SMON_TASK: Deadlock detection thread<br>\*   AXC_RCYTEMP_TASK: Temporary tablespace recovery thread |
| CELL_ID | INTEGER | Message queue ID corresponding to the task |
| PROCESS_MESSAGE_NUM | BIGINT | Number of messages processed |
| CORE_TASK | BOOLEAN | Is it a core thread? |