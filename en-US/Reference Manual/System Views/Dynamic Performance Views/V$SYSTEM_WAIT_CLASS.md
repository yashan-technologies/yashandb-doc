This view displays the statistics of all current wait event classes.

|Field |Type |Description |
| --- | --- | --- |
| WAIT\_CLASS\_ID | BIGINT | Wait event class ID |
| WAIT\_CLASS# | BIGINT | Wait event class number |
| WAIT\_CLASS | VARCHAR(32) | Wait event class name |
| TOTAL\_WAITS | BIGINT | Total number of waits |
| TIME\_WAITED | BIGINT | Total wait time (unit: microseconds) |
| TOTAL\_WAITS\_FG | BIGINT | Foreground wait count |
| TIME\_WAITED\_FG | BIGINT | Foreground wait time (unit: microseconds) |