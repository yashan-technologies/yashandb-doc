Fault Diagnosis View, showing the current information of all problems.

|Field |Type |Description |
| --- | --- | --- |
| PROBLEM_ID       | INTEGER    | The ID of the problem                              |
| PROBLEM_KEY      | VARCHAR(256) | The key of the current problem                     |
| FIRST_INCIDENT   | BIGINT     | Displays the ID of the first incident of the current problem |
| FIRSTINC_TIME    | DATE       | Displays the SCN of the first event occurrence of the current problem |
| LAST_INCIDENT    | BIGINT     | Displays the ID of the last incident of the current problem |
| LASTINC_TIME     | DATE       | Displays the SCN of the last event occurrence of the current problem |
| CUMULATIVE_NUMBER | BIGINT     | Displays the cumulative number of the current problem (excluding the number of flood control events) |