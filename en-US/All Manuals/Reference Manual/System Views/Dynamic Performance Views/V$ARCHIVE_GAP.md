This view shows the archived GAP intervals.

When a standby database recovers from an abnormal or non-operational state, it starts to receive and apply redo logs from the primary database's latest redo log. During the period of abnormal or non-operational state, if the primary database generates redo logs, they cannot be sent normally to the standby database. After the standby database recovers, its redo log files or archive log files may have discontinuous gaps, which are referred to as GAPs.

To repair the archived GAPs, the standby database needs to start the FAL thread to obtain the corresponding archive log files from the primary database.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | The ID of the archived GAP interval. If there are multiple discontinuous intervals, at least 2 rows will be output. |
| LOW_SEQUENCE# | INTEGER | The first sequence number (ASN) of the current archived GAP interval. |
| HIGH_SEQUENCE# | INTEGER | The last sequence number (ASN) of the current archived GAP interval. |