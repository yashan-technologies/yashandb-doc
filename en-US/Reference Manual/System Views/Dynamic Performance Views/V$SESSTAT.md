This view shows the statistics of all current sessions for the instance.

Difference from V$SYSSTAT: V$SYSSTAT records the cumulative values for all sessions, while V$SESSTAT records the statistics by session ID.

|Field |Type |Description |
| --- | --- | --- |
| SID         | SMALLINT  | Session ID                                                                  |
| STATISTIC#  | INTEGER   | Statistic ID, which identifies each statistic. The specific name of the corresponding statistic can be found in the V$STATNAME view using STATISTIC# |
| VALUE       | BIGINT    | Statistic value                                                             |