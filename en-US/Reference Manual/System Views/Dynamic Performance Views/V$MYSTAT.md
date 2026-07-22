This view displays the statistical information of the current session and shows a subset of V$SESSTAT, with SID corresponding to the SID in V$SESSION.

|Field |Type |Description |
| --- | --- | --- |
| SID          | SMALLINT  | Current session ID                                                                          |
| STATISTIC#   | INTEGER   | Statistic ID, which identifies each statistic. The corresponding name can be found in V$STATNAME view using STATISTIC# |
| VALUE        | BIGINT    | Statistical value                                                                           |