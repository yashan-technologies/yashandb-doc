This view shows the historical records of statistics collection operations performed using the DBMS_STATS advanced package and at the database level, not applicable for ISC Distributed Cluster Deployment.

|Field |Type |Description |
| --- | --- | --- |
| OPID      | BIGINT     | Internal ID of the statistics collection operation                                           |
| TARGET    | VARCHAR(512) | Name of the statistics collection target                                                      |
| TARGET_OBJ| BIGINT     | ID of the collection target                                                                   |
| TARGET_TYPE| VARCHAR(15)| Type of the collection target                                                                  |
| TARGET_SIZE| BIGINT     | Size of the target at the start of the collection task (number of blocks)                   |
| START_TIME| TIMESTAMP(6)  | Start time                                                                                   |
| END_TIME  | TIMESTAMP(6)  | End time                                                                                     |
| STATUS    | VARCHAR(11) | Status of the statistics collection operation<br>\*   IN PROGRESS: Running<br>\*   COMPLETED: Completed<br>\*   FAILED: Failed |
| PRIORITY  | INTEGER    | Ranking of the task among all target objects in the statistics parent operation              |
| NOTES     | VARCHAR(4000)| Comments on the statistics collection operation, for example, statistics option values and failure information for operations with status FAILED |