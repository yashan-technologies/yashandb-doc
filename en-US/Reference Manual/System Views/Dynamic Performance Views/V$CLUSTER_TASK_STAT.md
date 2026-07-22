This view describes the relevant statistics of YAC background threads.

|Field |Type |Description |
| --- | --- | --- |
| TASK\_ID | INTEGER | Task ID |
| SESSION\_ID | INTEGER | Session ID |
| TASK\_TYPE | VARCHAR(32) | Task type<br>\*   AXC\_GRC\_TASK: GRC thread<br>\*   AXC\_GCS\_TASK: GCS thread<br>\*   AXC\_GLS\_TASK: GLS thread<br>\*   AXC\_REFORM\_TASK: Reform thread<br>\*   AXC\_BATCH\_TASK: Batch processing thread<br>\*   AXC\_BCST\_TASK: DDL type thread<br>\*   AXC\_AUX\_TASK: Auxiliary thread<br>\*   AXC\_SMON\_TASK: Deadlock detection thread<br>\*   AXC\_RCYTEMP\_TASK: Temporary tablespace recovery thread |
| CELL\_ID | INTEGER | Message queue ID corresponding to the task |
| PROCESS\_MESSAGE\_NUM | BIGINT | Number of messages processed |
| CORE\_TASK | BOOLEAN | Is it a core thread? |