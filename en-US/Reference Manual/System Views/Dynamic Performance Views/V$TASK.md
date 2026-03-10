Task view, displaying information about currently executing and waiting tasks.

|Field |Type |Description |
| --- |--------------| --- |
| ID             | BIGINT       | Task ID                      |
| PARENT_ID      | BIGINT       | Parent Task ID               |
| TYPE           | VARCHAR(32)  | Type of the current task     |
| PARENT_STEP    | TINYINT      | STEP value in the parent task |
| CURRENT_STEP   | TINYINT      | The step currently being executed |
| TOTAL_STEP     | TINYINT      | Total number of steps in the current task |
| STATUS         | VARCHAR(32)  | Task status                  |
| DATA           | JSON         | Data content                 |
| LAST_ERROR     | VARCHAR(128) | Last error information       |
| MAX_TRY_TIMES  | TINYINT      | Maximum retry times          |
| FAIL_TIMES     | TINYINT      | Number of failures           |
| CREATE_TIME    | TIMESTAMP    | Task creation time           |
| UPDATE_TIME    | TIMESTAMP    | Task refresh time            |
| EXECUTE_TIMES  | TINYINT      | Number of executions         |