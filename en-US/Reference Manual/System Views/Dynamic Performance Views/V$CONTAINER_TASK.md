This view displays detailed information about parallel execution tasks.

|Field |Type |Description |
|---------------|--------------|--------------------|
| ID        | TINYINT       | ID of the task                       |
| NAME      | VARCHAR(68)   | Container name                       |
| STATUS    | VARCHAR(32)   | Task running status:<br/> * SUCCESS: Run successfully<br/> * RUNNING: Running<br/> * FAILED: Task execution failed  |
| TYPE      | VARCHAR(32)   | Task type:<br/> * build database: Build database<br/> * switchover: Perform a planned switchover<br/> * failover:Perform failover       |
| START_TIME | TIMESTAMP(6)  | Task start time                    |
| END_TIME   | TIMESTAMP(6)  | Task end time                    |
| ERROR      | VARCHAR(8192)  | Error message description for task execution failure |