This view displays the history of statistics collection operations executed at the database level using the DBMS_STATS package and is not applicable to ISC Distributed Cluster Deployment.

|Field |Type |Description |
| --- | --- | --- |
| ID | BIGINT | Internal ID of the statistics collection operation |
| OPERATION | VARCHAR(64) | Name of the statistics collection operation |
| TARGET | VARCHAR(512) | Name of the statistics collection target |
| START_TIME | TIMESTAMP | Start time |
| END_TIME | TIMESTAMP | End time |
| STATUS | VARCHAR(11) | Status of the statistics collection operation<br>\*   IN PROGRESS: Running<br>\*   COMPLETED: Completed  <br>\*   FAILED: Failed |
| SESSION_ID | INTEGER | Session ID that triggered the statistics collection operation |
| NOTES | VARCHAR(4000) | Comments on the statistics collection operation, such as option values and failure information for operations with status FAILED |