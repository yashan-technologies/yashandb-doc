Fault Diagnosis View, which displays problem information for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID            | INTEGER   | Group ID                                    |
| GROUP_NODE_ID       | INTEGER   | Node ID within the group                    |
| PROBLEM_ID          | INTEGER   | ID of the problem                           |
| PROBLEM_KEY         | VARCHAR(256) | Key of the current problem                  |
| FIRST_INCIDENT      | BIGINT    | ID of the first event of the current problem |
| FIRSTINC_TIME       | DATE      | SCN of the first event occurrence of the current problem |
| LAST_INCIDENT       | BIGINT    | ID of the last event of the current problem  |
| LASTINC_TIME        | DATE      | SCN of the last event occurrence of the current problem |
| CUMULATIVE_NUMBER    | BIGINT    | Cumulative count of the current problem (excluding flood control event count) |