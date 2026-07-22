This view displays the archived GAP intervals.

When the standby database recovers from an abnormal or non-operational state, it starts receiving and applying redo logs directly from the latest redo log of the primary database. During the abnormal or non-operational state, if the primary database generates redo logs, they cannot be sent to the standby database properly. After the standby database recovers, its redo log files or archive log files may have discontinuous gaps, which are referred to as GAPs. The archived GAP needs to be repaired by starting the FAL thread on the standby database to retrieve the corresponding archive log files from the primary database.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| ID  | INTEGER | Archived GAP interval ID. If there are multiple non-continuous gaps, at least 2 rows will be output. |
| LOW_SEQUENCE# | INTEGER | The first sequence number (ASN) of the current archived GAP interval |
| HIGH_SEQUENCE# | INTEGER | The last sequence number (ASN) of the current archived GAP interval |