This view displays information about statistics, corresponding to the statistics in V$SYSSTAT.

|Field |Type |Description |
| --- | --- | --- |
| STATISTIC# | INTEGER    | Statistic ID                     |
| NAME       | VARCHAR(64)| Name of the system statistic     |
| CLASS      | INTEGER    | System statistic category<br>\*   1: User<br>\*   2: Redo<br>\*   4: Enqueue<br>\*   8: Cache<br>\*   16: OS<br>\*   32: Cluster<br>\*   64: SQL<br>\*   128: DEBUG |