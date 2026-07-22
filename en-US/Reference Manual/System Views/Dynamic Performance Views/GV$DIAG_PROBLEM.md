Fault Diagnosis View, displaying current information about all issues.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | NUMBER  | Group ID                                         |
| GROUP_NODE_ID    | NUMBER  | Node ID within the group                         |
| INST_ID          | NUMBER  | Instance ID                                      |
| PROBLEM_ID       | INTEGER | ID of the problem                                |
| PROBLEM_KEY      | VARCHAR(256) | Key of the current problem                     |
| FIRST_INCIDENT   | BIGINT  | ID of the first incident of the current problem  |
| FIRSTINC_TIME    | DATE    | SCN of the first event occurring for the current problem |
| LAST_INCIDENT    | BIGINT  | ID of the last incident of the current problem    |
| LASTINC_TIME     | DATE    | SCN of the last event occurring for the current problem  |
| CUMULATIVE_NUMBER | BIGINT  | Cumulative count of the current problem (not counting flood control events) |