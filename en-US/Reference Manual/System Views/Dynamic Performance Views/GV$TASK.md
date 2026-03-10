This view displays information about currently executing and waiting tasks.

|Field |Type |Description |
|-----------------|---------------|-------------|
| GROUP_ID         | NUMBER        | Group ID         |
| GROUP_NODE_ID    | NUMBER        | Node ID in the group |
| INST_ID          | NUMBER        | Instance ID      |
| ID               | BIGINT        | Task ID          |
| PARENT_ID        | BIGINT        | Parent Task ID   |
| TYPE             | VARCHAR(32)   | Type of current task |
| PARENT_STEP      | TINYINT       | STEP value in the parent task |
| CURRENT_STEP     | TINYINT       | Current step the task is in |
| TOTAL_STEP       | TINYINT       | Total number of steps in the current task |
| STATUS           | VARCHAR(32)   | Task status      |
| DATA             | JSON          | Data content     |
| LAST_ERROR       | VARCHAR(128)  | Last error message |
| MAX_TRY_TIMES    | TINYINT       | Retry times      |
| FAIL_TIMES       | TINYINT       | Failure times    |
| CREATE_TIME      | TIMESTAMP     | Task creation time |
| UPDATE_TIME      | TIMESTAMP     | Task refresh time |
| EXECUTE_TIMES    | TINYINT       | Execution times   |