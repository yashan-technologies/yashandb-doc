This view displays the execution and waiting task information for all nodes in the ISC distributed cluster.

|Field |Type |Description |
|-----------------| --- | --- |
| GROUP\_ID        | INTEGER  | Group ID            |
| GROUP\_NODE\_ID  | INTEGER  | Node ID in group    |
| ID               | BIGINT   | Task ID             |
| PARENT\_ID       | BIGINT   | Parent Task ID      |
| TYPE             | VARCHAR(32) | Current task type |
| PARENT\_STEP     | TINYINT  | STEP value in parent task |
| CURRENT\_STEP    | TINYINT  | Current step being executed |
| TOTAL\_STEP      | TINYINT  | Total steps in current task |
| STATUS           | VARCHAR(32) | Task status      |
| DATA             | JSON     | Data content        |
| LAST\_ERROR      | VARCHAR(128) | Last error message |
| MAX\_TRY\_TIMES  | TINYINT  | Retry count         |
| FAIL\_TIMES      | TINYINT  | Failure count       |
| CREATE\_TIME     | TIMESTAMP(6)| Task creation time  |
| UPDATE\_TIME     | TIMESTAMP(6)| Task refresh time   |
| EXECUTE\_TIMES   | TINYINT  | Execution count     |