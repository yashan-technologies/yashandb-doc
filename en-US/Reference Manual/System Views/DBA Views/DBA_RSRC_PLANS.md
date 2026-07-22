This view displays all resource plan information in the database.

|Field |Type |Description |
| --- | --- | --- |
| PLAN_ID                   | BIGINT        | Resource plan ID                   |
| PLAN                      | VARCHAR(64)   | Resource plan name                 |
| NUM_PLAN_DIRECTIVES       | BIGINT        | Number of directives included in the resource plan |
| CPU_METHOD                | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |
| MGMT_METHOD               | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |
| ACTIVE_SESS_POOL_MTH      | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |
| PARALLEL_DEGREE_LIMIT_MTH | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |
| QUEUING_MTH               | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |
| SUB_PLAN                  | VARCHAR(3)    | Whether the resource plan is a sub-plan: Yes (YES) or No (NO) |
| COMMENTS                  | VARCHAR(2000) | Annotation information for the resource plan |
| STATUS                    | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |
| MANDATORY                 | VARCHAR(1)    | For compatibility only, current value is fixed as `NULL` |