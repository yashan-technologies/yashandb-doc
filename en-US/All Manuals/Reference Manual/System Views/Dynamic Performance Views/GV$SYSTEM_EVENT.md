This view shows the current statistics of all waiting events. You can find the definitions and descriptions of specific waiting events in the [Waiting Events](../../Wait Events) document.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | NUMBER   | Group ID                                   |
| GROUP_NODE_ID     | NUMBER   | Node ID within the group                   |
| INST_ID           | NUMBER   | Instance ID                                |
| EVENT             | VARCHAR(32) | Name of the waiting event                  |
| TOTAL_WAITS       | BIGINT   | Total number of waits                      |
| TOTAL_TIMEOUTS    | BIGINT   | Total number of timeouts                   |
| TIME_WAITED       | BIGINT   | Wait time (unit: milliseconds)            |
| AVERAGE_WAIT      | NUMBER   | Average wait time (unit: milliseconds)    |
| TIME_WAITED_MICRO | BIGINT   | Wait time (unit: microseconds)            |
| TOTAL_WAITS_FG    | BIGINT   | Total number of foreground waits           |
| TOTAL_TIMEOUTS_FG  | BIGINT   | Total number of foreground timeouts        |
| TIME_WAITED_FG    | BIGINT   | Foreground wait time (unit: milliseconds) |
| AVERAGE_WAIT_FG   | NUMBER   | Average foreground wait time (unit: milliseconds) |
| TIME_WAITED_MICRO_FG | BIGINT | Foreground wait time (unit: microseconds) |
| EVENT_ID          | INTEGER  | Waiting event ID                           |
| WAIT_CLASS        | VARCHAR(16) | Waiting event category, derived from the WAIT_CLASS field value in [V$SYSTEM_WAIT_CLASS](V$SYSTEM_WAIT_CLASS) |