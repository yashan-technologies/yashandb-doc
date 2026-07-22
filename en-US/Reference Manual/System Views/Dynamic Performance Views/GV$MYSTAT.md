This view displays the statistical information of the current session and shows a subset of V$SESSTAT, where SID corresponds to the SID in V$SESSION.

|Field |Type |Description |
|--------------|----------| --- |
| GROUP_ID       | NUMBER    | Group ID     |
| GROUP_NODE_ID  | NUMBER    | Node ID within the group |
| INST_ID        | NUMBER    | Instance ID  |
| SID            | SMALLINT  | Current session ID |
| STATISTIC#     | INTEGER   | Statistic ID, identifies each statistic; the corresponding name of the statistic can be found in V$STATNAME view using STATISTIC# |
| VALUE          | BIGINT    | Statistic value |