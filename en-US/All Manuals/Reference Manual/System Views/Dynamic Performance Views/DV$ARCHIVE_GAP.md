This view displays the archive gap intervals for all nodes in an ISC distributed cluster. In situations where the primary/standby network is unstable, or the standby database is down for an extended period, the standby database may lag behind with a significant amount of redo. To accelerate the redo synchronization rate, the primary database directly sends the latest redo to the standby database. The missing redo in between is supplemented by the FAL thread, which sends archive log files to fill in the gaps. The archive log files that have not yet been supplemented are referred to as archive gaps.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| ID  | INTEGER | Archive gap interval ID. If the gap consists of multiple non-contiguous intervals, at least 2 rows will be output. |
| LOW_SEQUENCE# | INTEGER | The first sequence number (ASN) of the current archive gap interval |
| HIGH_SEQUENCE# | INTEGER | The last sequence number (ASN) of the current archive gap interval |