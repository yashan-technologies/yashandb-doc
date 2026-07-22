This view displays information related to the database flashback functionality.



This feature is not available in ISC Distributed Cluster Deployment, and this view is meaningless.



|Field |Type |Description |
| --- | --- | --- |
| OLDEST\_FLASHBACK\_SCN    | BIGINT  | The SCN corresponding to the oldest point in time that the database flashback can reach |
| OLDEST\_FLASHBACK\_TIME    | TIMESTAMP(6) | The timestamp corresponding to the oldest point in time that the database flashback can reach |
| RETENTION\_TARGET          | BIGINT  | The maximum retention duration of the database flashback log files (in minutes). Log files that exceed this duration are eligible for automatic cleanup |
| TOTAL\_FILE\_SIZE         | BIGINT  | The total space occupied by all log files of the current database flashback (in bytes) |
