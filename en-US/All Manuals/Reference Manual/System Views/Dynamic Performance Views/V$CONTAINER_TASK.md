This view displays detailed information about parallel execution tasks.

|Field |Type |Description |
|---------------|--------------|--------------------|
| ID        | TINYINT       | ID of the task                       |
| NAME      | VARCHAR(68)   | Name of the container                   |
| STATUS    | VARCHAR(32)   | Execution status of the task: <br/> * SUCCESS：Execution successful<br/> * RUNNING：Still running<br/> * FAILED：Execution failed |
| TYPE      | VARCHAR(32)   | Type of the task: <br/> * build database: Create a standby database on the standby database in a primary/standby high availability deployment environment<br/> * switchover: Switchover of the primary and standby database while the primary and standby database are synchronized normally<br/> * failover: Failover of the standby database when leader election is not enabled and the primary database is abnormal      |
| START_TIME | TIMESTAMP  | Start time of the task                  |
| END_TIME   | TIMESTAMP  | End time of the task                    |
| ERROR      | VARCHAR(8192)  | Error massage when the task execution failed |