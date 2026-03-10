This view displays the statistics of all current wait event classes.

|Field |Type |Description |
| --- | --- | --- |
| WAIT_CLASS_ID | BIGINT | Wait event class ID |
| WAIT_CLASS# | BIGINT | Wait event class number |
| WAIT_CLASS | VARCHAR(32) | Wait event class name |
| TOTAL_WAITS | BIGINT | Total number of waits |
| TIME_WAITED | BIGINT | Total wait time (unit: microseconds) |
| TOTAL_WAITS_FG | BIGINT | Foreground wait count |
| TIME_WAITED_FG | BIGINT | Foreground wait time (unit: microseconds) |