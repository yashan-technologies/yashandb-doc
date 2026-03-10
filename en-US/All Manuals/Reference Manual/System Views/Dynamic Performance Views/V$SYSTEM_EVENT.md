This view displays all current waiting event statistics. You can refer to the [Waiting Events](../../Wait Events) documentation for specific definitions and explanations of waiting events.

|Field |Type |Description |
| --- | --- | --- |
| EVENT          | VARCHAR(32) | Waiting event name                           |
| TOTAL_WAITS    | BIGINT    | Total number of waits                         |
| TOTAL_TIMEOUTS  | BIGINT    | Total number of timeouts                     |
| TIME_WAITED    | BIGINT    | Wait time (in milliseconds)                  |
| AVERAGE_WAIT    | NUMBER    | Average wait time (in milliseconds)          |
| TIME_WAITED_MICRO | BIGINT  | Wait time (in microseconds)                  |
| TOTAL_WAITS_FG | BIGINT    | Total number of foreground waits              |
| TOTAL_TIMEOUTS_FG | BIGINT  | Total number of foreground timeouts          |
| TIME_WAITED_FG | BIGINT    | Foreground wait time (in milliseconds)       |
| AVERAGE_WAIT_FG | NUMBER    | Average foreground wait time (in milliseconds) |
| TIME_WAITED_MICRO_FG | BIGINT | Foreground wait time (in microseconds)      |
| EVENT_ID       | INTEGER   | Waiting event ID                              |
| WAIT_CLASS      | VARCHAR(16) | Waiting event category, sourced from the WAIT_CLASS field in [V$SYSTEM_WAIT_CLASS](V$SYSTEM_WAIT_CLASS) |