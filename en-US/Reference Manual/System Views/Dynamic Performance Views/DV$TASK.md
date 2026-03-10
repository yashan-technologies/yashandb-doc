This view displays the execution and waiting task information for all nodes in the distributed cluster.

|Field |Type |Description |
|-----------------| --- | --- |
| GROUP_ID        | INTEGER  | Group ID            |
| GROUP_NODE_ID  | INTEGER  | Node ID in group    |
| ID               | BIGINT   | Task ID             |
| PARENT_ID       | BIGINT   | Parent Task ID      |
| TYPE             | VARCHAR(32) | Current task type |
| PARENT_STEP     | TINYINT  | STEP value in parent task |
| CURRENT_STEP    | TINYINT  | Current step being executed |
| TOTAL_STEP      | TINYINT  | Total steps in current task |
| STATUS           | VARCHAR(32) | Task status      |
| DATA             | JSON     | Data content        |
| LAST_ERROR      | VARCHAR(128) | Last error message |
| MAX_TRY_TIMES  | TINYINT  | Retry count         |
| FAIL_TIMES      | TINYINT  | Failure count       |
| CREATE_TIME     | TIMESTAMP| Task creation time  |
| UPDATE_TIME     | TIMESTAMP| Task refresh time   |
| EXECUTE_TIMES   | TINYINT  | Execution count     |