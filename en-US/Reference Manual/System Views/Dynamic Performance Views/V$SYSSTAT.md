This view is used to display system statistic item information, including the sum of statistic items for all sessions on the current instance.

|Field |Type |Description |
| --- | --- | --- |
| STATISTIC# | INTEGER   | Statistic item ID            |
| NAME     | VARCHAR(64) | System statistic item name   |
| CLASS    | INTEGER   | System statistic item category<br>* 1: User<br>* 2: Redo<br>* 4: Enqueue<br>* 8: Cache<br>* 16: OS<br>* 32: Cluster<br>* 64: SQL<br>* 128: DEBUG |
| VALUE    | BIGINT    | Statistic value               |