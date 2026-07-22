This view displays the details of parallel execution tasks.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | NUMBER | Group ID |
| GROUP\_NODE\_ID | NUMBER | Node ID within the group |
| INST_ID       | NUMBER       | Instance ID                   |
| ID  | TINYINT | Task ID |
| NAME      | VARCHAR(68)   | Container name                       |
| STATUS    | VARCHAR(32)   | Task running status:<br/> * SUCCESS: Run successfully<br/> * RUNNING: Running<br/> * FAILED: Task execution failed  |
| TYPE      | VARCHAR(32)   | Task type:<br/> * build database: Build database<br/> * switchover: Perform a planned switchover<br/> * failover:Perform failover       |
| START_TIME | TIMESTAMP(6)  | Task start time                    |
| END_TIME   | TIMESTAMP(6)  | Task end time                    |
| ERROR      | VARCHAR(8192)  | Error message description for task execution failure |