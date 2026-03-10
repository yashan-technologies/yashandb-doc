This view displays the system event statistics for all nodes in the distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID             | INTEGER   | Group ID                                         |
| GROUP_NODE_ID        | INTEGER   | Node ID within the group                         |
| EVENT                | VARCHAR(32)| Wait event name                                  |
| TOTAL_WAITS          | BIGINT    | Total number of waits                            |
| TOTAL_TIMEOUTS       | BIGINT    | Total number of timeouts                         |
| TIME_WAITED          | BIGINT    | Wait time (in milliseconds)                      |
| AVERAGE_WAIT         | NUMBER    | Average wait time (in milliseconds)             |
| TIME_WAITED_MICRO    | BIGINT    | Wait time (in microseconds)                     |
| TOTAL_WAITS_FG      | BIGINT    | Total number of foreground waits                 |
| TOTAL_TIMEOUTS_FG    | BIGINT    | Total number of foreground timeouts              |
| TIME_WAITED_FG       | BIGINT    | Foreground wait time (in milliseconds)          |
| AVERAGE_WAIT_FG      | NUMBER    | Foreground average wait time (in milliseconds)  |
| TIME_WAITED_MICRO_FG | BIGINT    | Foreground wait time (in microseconds)          |
| EVENT_ID             | INTEGER   | Wait event ID                                   |
| WAIT_CLASS           | VARCHAR(16)| Wait event category, derived from the WAIT_CLASS field value of [V$SYSTEM_WAIT_CLASS](V$SYSTEM_WAIT_CLASS) |